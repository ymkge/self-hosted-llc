---
name: kindle-publishing
description: Best practices, layout rules, and build pipeline knowledge for publishing high-quality reflowable Kindle eBooks (EPUB). Covers ASCII box avoidance, responsive card UI, Japanese typography, Kindle Previewer verification, and KDP metadata setup.
---

# Kindle電子書籍（EPUB）制作・出版 実践ナレッジガイド

Kindle（KDP）向けの電子書籍（リフロー型EPUB）を制作・執筆・ビルド・校正する際に、表示崩れを防ぎ、商業出版レベルの品質を最初から担保するための実践ナレッジベースです。

---

## 📌 1. 表示崩れを起こさない組版・レイアウト設計（重要）

### ❌ やってはいけないこと：等幅テキストASCII罫線（`┌───┐`）
- **現象**:
  - ````text ┌─────────┐ │ 要点 │ └─────────┘ ```` のようなテキスト罫線は、PC上のMarkdownプレビューでは綺麗に見えても、**Kindle端末の画面幅（特にスマートフォン）やユーザーの文字サイズ拡大によって途中で改行され、著しくレイアウト崩れを起こす**。
- **解決策**:
  - 原稿段階からセマンティックなMarkdown・HTMLタグを用いるか、ビルドパイプラインで自動的に**HTML/CSSカードコンポーネント（`.callout-card`）**に変換する。

---

## 🎨 2. Kindle専用レスポンシブ・コールアウトカード仕様

Kindleリーダー（Paperwhite、iOS/Android Kindleアプリ、Fireタブレット）のすべてで美しく伸縮するカードUIの標準CSSです。

```css
/* ベースカード */
.callout-card {
    margin: 1.5em 0;
    border-radius: 8px;
    border: 1.5px solid #CBD5E1;
    background-color: #F8FAFC;
    overflow: hidden;
}

/* ヘッダー帯 */
.callout-header {
    padding: 0.7em 1em;
    font-size: 1.0em;
    font-weight: bold;
    color: #1E293B;
    background-color: #F1F5F9;
    border-bottom: 1px solid #E2E8F0;
}

/* コンテンツエリア */
.callout-content {
    padding: 0.8em 1.1em;
}

.callout-row {
    margin: 0.45em 0 !important;
    font-size: 0.95em;
    line-height: 1.6;
    color: #334155;
}

/* テーマ別カラーバリエーション */
.callout-summary { border-color: #93C5FD; }
.callout-summary .callout-header { background-color: #EFF6FF; color: #1E40AF; border-bottom-color: #DBEAFE; }

.callout-checklist { border-color: #A7F3D0; }
.callout-checklist .callout-header { background-color: #ECFDF5; color: #065F46; border-bottom-color: #D1FAE5; }

.callout-warning { border-color: #FDE68A; }
.callout-warning .callout-header { background-color: #FEF3C7; color: #92400E; border-bottom-color: #FDE68A; }

.callout-message { border-color: #DDD6FE; }
.callout-message .callout-header { background-color: #F5F3FF; color: #5B21B6; border-bottom-color: #EDE9FE; }
```

---

## ⚙️ 3. ビルドスクリプト（Python）での自動置換ロジック

原稿内にASCII枠が含まれていても、ビルド時に自動でカード構造へ変換する関数パターン：

```python
import re

def convert_ascii_boxes_to_html(content):
    box_pattern = re.compile(r'```text\s*\n(┌.*?└.*?┘)\s*\n```', re.DOTALL)
    
    def replacer(match):
        box_text = match.group(1)
        lines = box_text.strip().split("\n")
        inner_lines = [
            l.strip()[1:-1].strip() if l.strip().startswith("│") and l.strip().endswith("│") else l.strip()
            for l in lines if not any(l.strip().startswith(c) for c in ["┌", "└", "├"])
        ]
        inner_lines = [l for l in inner_lines if l]
        if not inner_lines:
            return ""
        
        title = inner_lines[0]
        body_lines = inner_lines[1:]
        
        # テーマ判定
        if "要点" in title:
            box_type = "summary"
        elif any(k in title for k in ["チェック", "リスト", "タスク", "実務"]):
            box_type = "checklist"
        elif any(k in title for k in ["鉄則", "重要", "注意", "落とし穴", "ルール"]):
            box_type = "warning"
        elif any(k in title for k in ["著者", "メッセージ", "読者"]):
            box_type = "message"
        else:
            box_type = "default"
            
        html = [f'<div class="callout-card callout-{box_type}">']
        html.append(f'  <div class="callout-header">{title}</div>')
        if body_lines:
            html.append('  <div class="callout-content">')
            for bl in body_lines:
                html.append(f'    <p class="callout-row">{bl}</p>')
            html.append('  </div>')
        html.append('</div>')
        return "\n".join(html)

    return box_pattern.sub(replacer, content)
```

---

## 📱 4. Kindle Previewer 3 による検証チェックリスト

EPUBビルド後は、必ず **Kindle Previewer 3** にファイルをドラッグ＆ドロップし、以下の4項目を確認すること：

1. **スマートフォン表示モード**:
   - 画面幅が最も狭い状態で、文字の不自然な改行や枠線の突き抜けがないか。
2. **フォントサイズ最大化テスト**:
   - フォントサイズを「大」にしても、表やカードが綺麗にレスポンシブに追従するか。
3. **画像の収まり**:
   - 画面幅を突き破らず、適切な余白とキャプション（`▲ ...`）が表示されているか。
4. **目次ナビゲーション（TOC）**:
   - 左側メニューの目次から全章へジャンプできるか。
