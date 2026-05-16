---
name: video-summary
description: "Given a YouTube channel URL, fetches the channel's recent uploads, filters for videos posted within a user-specified timeframe (default is last 24 hours), downloads their subtitles via yt-dlp, and produces a bilingual (English + Chinese) summary for each. Use this skill whenever the user wants to summarise new videos from a YouTube channel, check a channel for recent uploads, or get a digest of a creator's content over any time window. Trigger on phrases like \"summarise new videos\", \"check for new videos from this channel\", \"daily video summary\", \"what did [channel] post this week\", or any mention of a YouTube channel URL combined with summarisation."
---

## Overview

You will:
1. Ask the user for a YouTube channel URL and timeframe if not already provided
2. Use yt-dlp to list the channel's recent uploads
3. Filter for videos uploaded within the specified timeframe (in the user's local timezone)
4. For each new video, download and clean its subtitles
5. Print a bilingual English + Chinese summary (≤600 words each) for each video

---

## Step 1 — Ask for the channel URL and timeframe (if not already provided)

Check what the user has given you:
- If no channel URL → ask for it
- If no timeframe mentioned → use **last 24 hours** as the default (do not ask, just proceed)
- If they specified a timeframe (e.g. "last 3 days", "past week", "last 48 hours") → use that

Convert the timeframe to a number of hours. Examples:
- "last 24 hours" → 24
- "last 48 hours" / "last 2 days" → 48
- "past week" / "last 7 days" → 168
- "last 3 days" → 72

Accepted channel formats: `https://www.youtube.com/@handle`, `https://www.youtube.com/channel/ID`, or any valid YouTube channel URL.

---

## Step 2 — Fetch recent videos from the channel

Use yt-dlp to list recent uploads and filter by the timeframe. Replace `<CHANNEL_URL>` with the user's URL and `<HOURS>` with the number of hours (e.g. `24`, `48`, `168`):

```bash
python3 - <<'EOF'
import subprocess, sys, time
from datetime import datetime

channel_url = "<CHANNEL_URL>"
hours = <HOURS>
cutoff_ts = time.time() - (hours * 3600)
local_tz = datetime.now().astimezone().tzinfo

# Scale playlist scan size with the timeframe so longer windows don't miss videos
playlist_end = max(10, hours // 12)

result = subprocess.run(
    [
        "yt-dlp",
        "--print", "%(timestamp)s\t%(title)s\t%(webpage_url)s",
        "--playlist-end", str(playlist_end),
        channel_url,
    ],
    capture_output=True, text=True
)

if result.returncode != 0:
    print("ERROR: Could not fetch channel videos")
    print(result.stderr[:300])
    sys.exit(1)

new_videos = []
for line in result.stdout.strip().splitlines():
    parts = line.split('\t', 2)
    if len(parts) != 3:
        continue
    ts_str, title, url = parts
    try:
        ts = float(ts_str)
        if ts >= cutoff_ts:
            upload_dt = datetime.fromtimestamp(ts, tz=local_tz)
            new_videos.append({
                'date': upload_dt.strftime('%Y-%m-%d %H:%M %Z'),
                'title': title.strip(),
                'url': url.strip()
            })
    except (ValueError, TypeError):
        continue

if not new_videos:
    print("NO_NEW_VIDEOS")
else:
    for v in new_videos:
        print(f"{v['date']}\t{v['title']}\t{v['url']}")
EOF
```

If the output is `NO_NEW_VIDEOS`, stop immediately and tell the user: "No new videos found on this channel in the last N hours."

---

## Step 3 — Download and clean subtitles for each new video

For each video, run yt-dlp to download the English subtitles and clean the VTT output in one step:

```bash
python3 - <<'EOF'
import subprocess, re, sys, tempfile, os

video_url = "<VIDEO_URL>"

with tempfile.TemporaryDirectory() as tmpdir:
    result = subprocess.run(
        [
            "yt-dlp",
            "--skip-download",
            "--write-sub",
            "--write-auto-sub",
            "--sub-lang", "en",
            "--sub-format", "vtt",
            "--output", os.path.join(tmpdir, "%(title)s.%(ext)s"),
            video_url,
        ],
        capture_output=True, text=True
    )

    vtt_files = [f for f in os.listdir(tmpdir) if f.endswith(".vtt")]
    if not vtt_files:
        print("ERROR: No subtitle file found")
        sys.exit(1)

    raw = open(os.path.join(tmpdir, vtt_files[0]), encoding="utf-8").read()

# Strip WebVTT header
raw = re.sub(r'^WEBVTT.*?\n\n', '', raw, flags=re.DOTALL)
# Strip timestamp lines
raw = re.sub(r'\d{2}:\d{2}:\d{2}\.\d{3} --> \d{2}:\d{2}:\d{2}\.\d{3}.*\n', '', raw)
# Strip inline timestamps
raw = re.sub(r'<\d{2}:\d{2}:\d{2}\.\d{3}>', '', raw)
# Strip HTML tags
raw = re.sub(r'<[^>]+>', '', raw)
# Strip cue numbers and NOTE lines
raw = re.sub(r'^\d+\s*$', '', raw, flags=re.MULTILINE)
raw = re.sub(r'^NOTE.*$', '', raw, flags=re.MULTILINE)

lines = [l.strip() for l in raw.splitlines() if l.strip()]
deduped = []
for line in lines:
    if not deduped or line != deduped[-1]:
        deduped.append(line)

print('\n'.join(deduped))
EOF
```

Replace `<VIDEO_URL>` with the actual video URL. Run one video at a time.

If the output is `ERROR: No subtitle file found`, note that subtitles were unavailable for that video and continue to the next — do not stop entirely.

---

## Step 4 — Summarise

Using the cleaned transcript, produce a summary with this structure:

```
## [Video Title]
Date: [YYYY-MM-DD]
URL: [video URL]

### Summary (English)
**Main topic:** [one sentence]

**Key points:**
- [point 1]
- [point 2]
- [point 3]
- ...

[2–3 sentence closing synthesis]

---

### 摘要（中文）
**主题：** [一句话概括]

**要点：**
- [要点 1]
- [要点 2]
- [要点 3]
- ...

[2–3句总结]

---
```

Keep each language version under 600 words. Use plain, readable language. Focus on what the speaker actually argued or demonstrated, not just the topic.

After all videos are processed, print a one-line count:

> "Processed N video(s) from [channel URL] in the last X hours."

---

## Notes

- Filtering uses `%(timestamp)s` (Unix timestamp) — this gives exact upload time, so the 24-hour window is precise regardless of what calendar date it falls on in any timezone.
- Dates are displayed in the user's local system timezone via `datetime.now().astimezone().tzinfo`.
- `playlist_end` scales automatically with the timeframe (`max(10, hours // 12)`) so longer windows scan more videos without being wasteful.
- `--flat-playlist` is intentionally NOT used — it returns `NA` for timestamps on many channels. Fetching full metadata is slightly slower but reliable.
- Process videos sequentially — one at a time.
- `tempfile.TemporaryDirectory()` cleans up subtitle files automatically after each video.
- If `yt-dlp` is not installed, tell the user: "yt-dlp is required. Install with: `pip install yt-dlp` or `brew install yt-dlp`."
