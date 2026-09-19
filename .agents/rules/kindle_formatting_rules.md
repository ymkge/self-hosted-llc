---
description: Rules for Kindle eBook (EPUB) manuscript writing, formatting, and responsive typography
globs: ["books/**", "manuscripts/**", "**/*.epub"]
---

# Kindle電子書籍（リフロー形式）執筆・フォーマット設計ルール

Kindle電子書籍（リフロー型EPUB）の執筆・ビルド・校正を行う際は、以下のルールを必ず遵守してください。

## 1. 端末依存の「テキストASCII枠（罫線）」は絶対禁止
- **禁止パターン**:
  - 等幅テキストによるボックス枠（例: `┌───┐`, `│...│`, `└───┘`, `+---+`, `|---|` 等）。
  - Kindle端末の画面幅（スマートフォン、タブレット、Kindle Paperwhite等）や読者のフォントサイズ・書体変更によって**必ず改行・表示崩れが発生**します。
- **推奨パターン**:
  - 原稿上は通常のMarkdown構造（見出し、箇条書き、引用 `>` など）で記述するか、ビルド時に**セマンティックなHTMLカードUI（`.callout-card`）へと自動変換**する。

## 2. コールアウト・要点カードの設計標準
要点まとめやチェックリスト、警告、メッセージ等の枠組みは、以下のCSSクラスを用いたHTML/CSSカードとして構成する。

```html
<div class="callout-card callout-{theme}">
  <div class="callout-header">📌 タイトル・アイコン</div>
  <div class="callout-content">
    <p class="callout-row">1. 項目内容...</p>
    <p class="callout-row">2. 項目内容...</p>
  </div>
</div>
```

### テーマ分類（カラー・デザイン）
- `callout-summary`（要点まとめ・要約）:
  - 枠線: `#93C5FD` / ヘッダー背景: `#EFF6FF` / ヘッダー文字: `#1E40AF`
- `callout-checklist`（タスク一覧・チェックシート）:
  - 枠線: `#A7F3D0` / ヘッダー背景: `#ECFDF5` / ヘッダー文字: `#065F46`
- `callout-warning`（注意・警告・鉄則・落とし穴）:
  - 枠線: `#FDE68A` / ヘッダー背景: `#FEF3C7` / ヘッダー文字: `#92400E`
- `callout-message`（著者・制作チームからのメッセージ・案内）:
  - 枠線: `#DDD6FE` / ヘッダー背景: `#F5F3FF` / ヘッダー文字: `#5B21B6`

## 3. レスポンシブ組版（CSS）のベストプラクティス
- **pre / コードブロック**:
  - 画面幅を突き抜けないよう `white-space: pre-wrap;` および `word-break: break-all;` を必須適用する。
- **テーブル（表）**:
  - スマホでの横はみ出しを防ぐため、フォントサイズを `0.85em` 程度に縮小し、パディングを `0.5em 0.7em` 程度に引き締める。
- **画像（インフォグラフィック・挿絵）**:
  - `max-width: 100%; height: auto; display: block; margin: 1.5em auto;` を適用。
  - 画像直下には `.image-caption`（`font-size: 0.85em; text-align: center; color: #718096;`）を配置する。

## 4. 自動変換パイプラインの標準実装
ビルドスクリプト（`build_epub.py`）内で、万一テキスト罫線が含まれていた場合でも自動で `.callout-card` へ置換・正規化する変換関数を常備すること。

## 5. 日本語の括弧類（「」『』等）と太字（**）の解釈トラブル防止
- **原因**:
  - Markdown（CommonMark仕様）のパーサーは、日本語の約物（「」『』（）【】等）を単語境界（Word Boundary）として正しく判定できず、直前直後に日本語文字が隣接していると `**「〜」**` が太字にならずそのまま `**` が残るバグ（Intra-word emphasis issue）が発生する。
- **対処法**:
  - ビルドスクリプト内で、Markdownレンダリング直前にコードブロック以外の `**...**` を `<strong>...</strong>` へと正規化・自動変換するパイプライン処理を必ず通すこと。
