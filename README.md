# PDF 頁邊短評

`pdf-margin-review` 是一個 Codex skill，可以把對 PDF 的評語排到頁面右側。它會保留原始 PDF，另存一份副本，在原句上加黃色螢光標記，並在右側白色卡片顯示「編號」、「原文」和「說明」。

卡片使用 LXGW WenKai TC。編號與原文為紅色，說明為深灰色。標記和卡片直接畫入頁面，開啟 PDF 就能看到。

## 在 Codex 使用

在 Codex 開啟本專案並附上 PDF，即可用下方的提示詞開始。

> 用 `$pdf-margin-review` 幫我檢查這份 PDF 的教學目標與評量方式，並加上頁邊短評。

你也可以直接提供要標記的原句與說明，讓 skill 套用同一套版面。完整流程見 [SKILL.md](.agents/skills/pdf-margin-review/SKILL.md)。

## 直接執行腳本

建議使用已驗證的 Python 3.12。在專案根目錄安裝相依套件。

```sh
python3 -m pip install pdfplumber pypdf reportlab
```

將短評存成 `annotations.json`。把下方的 `quote` 換成 PDF 裡的原句，`page` 使用從 1 開始的實際頁序。

```json
{
  "annotations": [
    {
      "page": 1,
      "quote": "學生能用本課句型分享飲食喜好",
      "comment": "可補上達標條件，例如每位學生用三個句型各說一句。"
    }
  ]
}
```

若同一句在同頁出現多次，可加入 `"occurrence": 2` 來指定第二個符合位置。

```sh
python3 .agents/skills/pdf-margin-review/scripts/render_margin_review.py \
  original.pdf annotations.json review.pdf
```

腳本會產生 `review.pdf` 和 `review.annotations.json`。新標注使用實際建立當下的台北時間，格式為 `YYYYMMDDHHmmss-N`。字型與 [OFL 授權](.agents/skills/pdf-margin-review/assets/OFL.txt) 已隨專案附上。

## 重排與限制

重排時使用同一份原始 PDF 和輸出的標注清單，並指定新檔名。清單中的既有 `id` 會保留，新增的標注可省略 `id`。

```sh
python3 .agents/skills/pdf-margin-review/scripts/render_margin_review.py \
  original.pdf review.annotations.json review-v2.pdf
```

本版支援可選取文字、未加密、未旋轉且未裁切的 PDF。掃描件須先建立可靠的文字層。若同頁卡片加寬後仍排不下，腳本會指出需要縮短說明或減少標注的頁面。
