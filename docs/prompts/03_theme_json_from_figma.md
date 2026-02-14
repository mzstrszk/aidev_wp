# 03 — Figma デザイントークン → theme.json 変換

> **前提**: `00_rules.md` 読み込み済み、`02` でスキャフォールド済み
> **成果物**: Figma のデザイン仕様を反映した完全な `theme.json`

---

## このプロンプトの目的

Figma で定義したデザイントークン（カラー、タイポグラフィ、スペーシング等）を
`theme.json` に変換し、テーマのスタイル基盤を確立する。

**`theme.json` がデザインの Single Source of Truth になる。**

---

## 事前準備（人間がやること）

### 1. Figma からデザイントークンを抽出する

以下の情報を Figma から読み取り、下の「入力データ」セクションに記入する。

- カラーパレット（名前 + Hex 値）
- フォント（ファミリー名、ウェイト、用途）
- フォントサイズスケール
- スペーシングスケール
- レイアウト幅（コンテンツ幅、ワイド幅）
- 角丸（border-radius）の値

### 2. Figma のスタイルガイド画面をスクリーンショット

`docs/figma/` に保存し、プロンプトと一緒に渡すと精度が上がる。

---

## 入力データ（ここを埋める）

```yaml
# --- カラー ---
colors:
  primary:    "#______"
  secondary:  "#______"
  accent:     "#______"
  background: "#______"
  foreground: "#______"
  muted:      "#______"   # 薄いグレー等

# --- フォント ---
fonts:
  heading:
    family: "______"
    weights: [400, 700]
    source: google  # google / local / system
  body:
    family: "______"
    weights: [400, 500, 700]
    source: google

# --- フォントサイズ ---
font_sizes:
  small:    "14px"
  medium:   "16px"
  large:    "20px"
  x-large:  "28px"
  xx-large: "36px"
  # 必要に応じて追加

# --- スペーシング ---
spacing:
  "10": "0.625rem"   # 10px
  "20": "1rem"       # 16px
  "30": "1.5rem"     # 24px
  "40": "2rem"       # 32px
  "50": "3rem"       # 48px
  "60": "4rem"       # 64px

# --- レイアウト ---
layout:
  content_width: "720px"
  wide_width:    "1200px"

# --- 角丸 ---
border_radius:
  small:  "4px"
  medium: "8px"
  large:  "16px"
  full:   "9999px"
```

---

## AI への指示

上記の入力データを元に、`theme/theme.json` を**完全な形で**生成してください。

### 要件

1. **`$schema`** を指定する
2. **`version`**: `3`（WP 6.6+）
3. **`settings`** セクション
   - `color.palette`: 入力データのカラーを `theme` オリジンで登録
   - `color.gradients`: 必要に応じて（なくても可）
   - `typography.fontFamilies`: Google Fonts の場合は `fontFace` で `src` を指定
   - `typography.fontSizes`: 入力データのスケールを登録（`fluid` 対応推奨）
   - `spacing.spacingSizes`: 入力データのスペーシングを登録
   - `layout`: `contentSize`, `wideSize`
   - `appearanceTools: true`
   - `useRootPaddingAwareAlignments: true`
   - 不要なUI要素のオフ（`color.custom: false` 等は任意）

4. **`styles`** セクション
   - `color.background` / `color.text` をルートに設定
   - `typography` にデフォルトのフォントファミリー・サイズを設定
   - `spacing.padding` でルートのパディングを設定
   - `elements` で `h1`〜`h6`, `link`, `button` のスタイルを定義
   - `blocks` で主要コアブロックのデフォルトスタイルを定義
     - `core/button`, `core/navigation`, `core/post-title`, `core/query-title` 等

5. **`customTemplates`**（任意）
   - `front-page`, `blank`（余白なし）等

6. **`templateParts`**
   - `header`（area: `header`）
   - `footer`（area: `footer`）

### 出力形式

- `theme/theme.json` のファイルパスを明示して完全な JSON を出力
- JSON として valid であること（末尾カンマなし）
- コメントは JSON 内に書けないため、各セクションの意図は出力後に別途説明

---

## 確認ポイント

- [ ] `theme.json` が JSON として valid
- [ ] サイトエディタ →「スタイル」でカラーパレットが表示される
- [ ] サイトエディタ →「スタイル → タイポグラフィ」でフォントが選択できる
- [ ] Google Fonts を使用している場合、フォントが正しく読み込まれる
- [ ] フロントエンドでカラー・フォント・スペーシングが反映されている
- [ ] レイアウト幅（コンテンツ幅・ワイド幅）が意図通り

---

## Figma スクリーンショット添付テンプレート

> 添付画像は Figma のスタイルガイドです。
> この画像から読み取れるカラー・フォント・スペーシングの情報を
> 上の入力データ形式で抽出してから、theme.json を生成してください。

---

## 次のステップ

`04_template_generate.md` でテンプレートにスタイルを適用し、レイアウトを作り込む。