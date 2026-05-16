# video-summary

A Claude skill that monitors a YouTube channel and produces summaries of recent videos — always in English, plus whatever language you write your prompt in.

---

## Table of Contents
- [English](#english)
- [Español](#español)
- [简体中文](#简体中文)
- [繁體中文](#繁體中文)
- [日本語](#日本語)

---

## English

### What it does
Give Claude a YouTube channel URL and it will:
1. Fetch the channel's recent uploads
2. Filter for videos posted within your specified timeframe (default: last 24 hours)
3. Download the English subtitles for each video
4. Produce a summary in **English + your prompt language** for each one

If you write your prompt in Japanese, you get English + Japanese. In Spanish, English + Spanish. In English, English only.

### Prerequisites
Install [yt-dlp](https://github.com/yt-dlp/yt-dlp) before using this skill:
```bash
pip install yt-dlp
# or
brew install yt-dlp
```

### Installation
1. Download `video-summary.skill`
2. In Claude Cowork, open the Skills panel and upload the file

### Usage
Just type naturally — no special commands needed:

```
Summarise new videos from https://www.youtube.com/@channelname
```
```
Check https://www.youtube.com/@channelname for videos in the last 3 days
```
```
What did https://www.youtube.com/@channelname post this week?
```

If you don't specify a timeframe, the skill defaults to the **last 24 hours**.

### Output format
For each video found, you'll get:
- Video title, date (in your local timezone), and URL
- English summary with main topic and key points (under 600 words)
- Summary in your prompt language with the same content (under 600 words), if different from English
- A final count of how many videos were processed

---

## Español

### Qué hace
Proporciona a Claude la URL de un canal de YouTube y este:
1. Obtendrá las últimas subidas del canal
2. Filtrará los videos publicados dentro del período indicado (por defecto: últimas 24 horas)
3. Descargará los subtítulos en inglés de cada video
4. Generará un resumen en **inglés + el idioma de tu mensaje** para cada uno

Si escribes en español, obtienes inglés + español. En japonés, inglés + japonés. En inglés, solo inglés.

### Requisitos previos
Instala [yt-dlp](https://github.com/yt-dlp/yt-dlp) antes de usar esta skill:
```bash
pip install yt-dlp
# o
brew install yt-dlp
```

### Instalación
1. Descarga el archivo `video-summary.skill`
2. En Claude Cowork, abre el panel de Skills y sube el archivo

### Uso
Escribe de forma natural — no se necesitan comandos especiales:

```
Resume los videos nuevos de https://www.youtube.com/@nombredelcanal
```
```
Revisa https://www.youtube.com/@nombredelcanal para videos de los últimos 3 días
```
```
¿Qué publicó https://www.youtube.com/@nombredelcanal esta semana?
```

Si no especificas un período de tiempo, la skill usará las **últimas 24 horas** por defecto.

### Formato de salida
Por cada video encontrado recibirás:
- Título, fecha (en tu zona horaria local) y URL del video
- Resumen en inglés con tema principal y puntos clave (menos de 600 palabras)
- Resumen en español con el mismo contenido (menos de 600 palabras)
- Un conteo final de cuántos videos fueron procesados

---

## 简体中文

### 功能介绍
向 Claude 提供一个 YouTube 频道 URL，它将会：
1. 获取该频道的最新上传视频
2. 筛选在指定时间范围内发布的视频（默认：过去 24 小时）
3. 下载每个视频的英文字幕
4. 为每个视频生成**英文 + 你使用的语言**的摘要

用中文提问，获得英文 + 简体中文摘要。用日文提问，获得英文 + 日文摘要。用英文提问，仅输出英文。

### 前提条件
使用此技能前，请先安装 [yt-dlp](https://github.com/yt-dlp/yt-dlp)：
```bash
pip install yt-dlp
# 或
brew install yt-dlp
```

### 安装方法
1. 下载 `video-summary.skill` 文件
2. 在 Claude Cowork 中打开 Skills 面板并上传该文件

### 使用方式
直接用自然语言输入，无需特殊命令：

```
总结 https://www.youtube.com/@频道名称 的最新视频
```
```
查看 https://www.youtube.com/@频道名称 过去 3 天的视频
```
```
https://www.youtube.com/@频道名称 这周发了什么？
```

如果未指定时间范围，技能默认使用**过去 24 小时**。

### 输出格式
对于找到的每个视频，您将获得：
- 视频标题、日期（按您的本地时区）和链接
- 英文摘要，包含主要主题和要点（600 字以内）
- 简体中文摘要，内容相同（600 字以内）
- 处理视频数量的最终统计

---

## 繁體中文

### 功能介紹
向 Claude 提供一個 YouTube 頻道 URL，它將會：
1. 獲取該頻道的最新上傳影片
2. 篩選在指定時間範圍內發布的影片（預設：過去 24 小時）
3. 下載每個影片的英文字幕
4. 為每個影片生成**英文 + 你使用的語言**的摘要

用繁體中文提問，獲得英文 + 繁體中文摘要。用日文提問，獲得英文 + 日文摘要。用英文提問，僅輸出英文。

### 前提條件
使用此技能前，請先安裝 [yt-dlp](https://github.com/yt-dlp/yt-dlp)：
```bash
pip install yt-dlp
# 或
brew install yt-dlp
```

### 安裝方法
1. 下載 `video-summary.skill` 檔案
2. 在 Claude Cowork 中開啟 Skills 面板並上傳該檔案

### 使用方式
直接用自然語言輸入，無需特殊指令：

```
摘要 https://www.youtube.com/@頻道名稱 的最新影片
```
```
查看 https://www.youtube.com/@頻道名稱 過去 3 天的影片
```
```
https://www.youtube.com/@頻道名稱 這週發布了什麼？
```

如果未指定時間範圍，技能預設使用**過去 24 小時**。

### 輸出格式
對於找到的每個影片，您將獲得：
- 影片標題、日期（按您的本地時區）和連結
- 英文摘要，包含主要主題和要點（600 字以內）
- 繁體中文摘要，內容相同（600 字以內）
- 處理影片數量的最終統計

---

## 日本語

### 機能説明
YouTube チャンネルの URL を Claude に伝えると、以下を実行します：
1. チャンネルの最新動画を取得
2. 指定した期間内に投稿された動画を絞り込む（デフォルト：過去 24 時間）
3. 各動画の英語字幕をダウンロード
4. 各動画の**英語 + あなたが使った言語**の要約を生成

日本語でプロンプトを書くと、英語 + 日本語の要約が得られます。スペイン語なら英語 + スペイン語。英語なら英語のみ。

### 必要条件
このスキルを使用する前に [yt-dlp](https://github.com/yt-dlp/yt-dlp) をインストールしてください：
```bash
pip install yt-dlp
# または
brew install yt-dlp
```

### インストール方法
1. `video-summary.skill` ファイルをダウンロード
2. Claude Cowork で Skills パネルを開き、ファイルをアップロード

### 使い方
特別なコマンドは不要です。自然な文章で入力してください：

```
https://www.youtube.com/@チャンネル名 の新しい動画を要約して
```
```
https://www.youtube.com/@チャンネル名 の過去3日間の動画を確認して
```
```
https://www.youtube.com/@チャンネル名 は今週何を投稿した？
```

期間を指定しない場合、スキルは**過去 24 時間**をデフォルトとして使用します。

### 出力形式
見つかった各動画について以下が出力されます：
- 動画のタイトル、日付（ローカルタイムゾーン）、URL
- 英語の要約（メインテーマと要点、600 語以内）
- 日本語の要約（同じ内容、600 語以内）
- 処理した動画数の最終カウント

---

## License

MIT
