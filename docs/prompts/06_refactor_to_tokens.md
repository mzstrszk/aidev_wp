# 06 — デザイントークンへのリファクタリング

> **前提**: `00_rules.md` 読み込み済み、テンプレート・パターンが一通り動作していること
> **成果物**: ハードコード値を排除し、`theme.json` トークンに統一されたテーマ

---

## このプロンプトの目的

開発を進める過程で混入した**ハードコード値**（直接指定のカラーコード、px 値、フォント名等）を
洗い出し、すべて `theme.json` のプリセット値に置き換える。

これにより:
- `theme.json` を変更するだけでテーマ全体のスタイルが更新できる
- サイトエディタのグローバルスタイル機能と完全に連動する
- 保守性・拡張性が大幅に向上する

---

## AI への指示

### ステップ 1: ハードコード値の検出

以下のファイルを読み込み、`theme.json` のプリセットに置き換えるべき箇所をリストアップしてください。

**対象ファイル:**
- `theme/templates/*.html`（全テンプレート）
- `theme/parts/*.html`（全パーツ）
- `theme/patterns/*.php`（全パターン）
- `theme/assets/css/*.css`（追加 CSS があれば）
- `theme/functions.php`

**検出対象:**

| 種別 | ハードコード例 | 置き換え先 |
|---|---|---|
| カラー | `#1a1a2e`, `rgb(...)`, `style` 内の color 指定 | `var(--wp--preset--color--{slug})` |
| フォントサイズ | `18px`, `1.2rem`, `fontSize` 属性の直値 | `var(--wp--preset--font-size--{slug})` |
| フォントファミリー | `"Noto Sans JP"` 直指定 | `var(--wp--preset--font-family--{slug})` |
| スペーシング | `32px`, `2rem` のパディング/マージン | `var(--wp--preset--spacing--{slug})` |
| 角丸 | `8px`, `border-radius` 直値 | `var(--wp--custom--border-radius--{slug})` |
| 幅 | `720px`, `1200px` 直指定 | レイアウト設定に委譲 |

**出力形式:**

```markdown
## 検出結果

### {ファイルパス}
| 行 | 現在の値 | 置き換え先 | 備考 |
|---|---|---|---|
| L15 | `"customBackgroundColor":"#1a1a2e"` | `"backgroundColor":"primary"` | プリセットカラーに変更 |
| ... | ... | ... | ... |
```

---

### ステップ 2: 一括リファクタリング

検出結果をもとに、各ファイルの修正版を出力してください。

**要件:**
- プリセットに対応する値がない場合は、`theme.json` に追加してください
- ブロック属性の書き方に注意:
  - ❌ `"style":{"color":{"background":"#1a1a2e"}}`
  - ✅ `"backgroundColor":"primary"`
  - ❌ `"style":{"typography":{"fontSize":"20px"}}`
  - ✅ `"fontSize":"large"`
  - ❌ `"style":{"spacing":{"padding":{"top":"32px"}}}`
  - ✅ `"style":{"spacing":{"padding":{"top":"var:preset|spacing|40"}}}`
- CSS カスタムプロパティの書式:
  - HTML ブロック属性内: `var:preset|color|primary`
  - CSS ファイル内: `var(--wp--preset--color--primary)`

---

### ステップ 3: 不要な CSS の削除

`theme.json` のプリセットで対応できるようになったスタイルが
`assets/css/` に残っていれば、削除または統合してください。

**判断基準:**
- `theme.json` の `styles.blocks` で指定できる → CSS 不要
- `theme.json` の `styles.elements` で指定できる → CSS 不要
- 疑似要素（`::before`, `::after`）やアニメーション → CSS が必要
- `:hover`, `:focus` のカスタムスタイル → 一部は theme.json で対応可、それ以外は CSS

---

## theme.json にトークンを追加する場合のガイドライン

`settings.custom` に独自トークンを追加できる:

```json
{
  "settings": {
    "custom": {
      "borderRadius": {
        "small": "4px",
        "medium": "8px",
        "large": "16px"
      },
      "transition": {
        "duration": "0.3s",
        "easing": "ease-in-out"
      },
      "lineHeight": {
        "tight": "1.3",
        "normal": "1.6",
        "loose": "1.8"
      }
    }
  }
}
```

これらは `var(--wp--custom--border-radius--medium)` 等で参照可能。

---

## 確認ポイント

- [ ] テンプレート・パターン内に Hex カラーコードが残っていない
- [ ] `px` で直指定されたフォントサイズが残っていない
- [ ] フォントファミリーが直接文字列で書かれていない
- [ ] `theme.json` を変更すると、全ページに即座に反映される
- [ ] サイトエディタの「スタイル」パネルから変更した内容が正しく反映される
- [ ] リファクタリング前後でフロントエンドの見た目が変わっていない（ビジュアルリグレッション）
- [ ] `assets/css/` に不要なスタイルが残っていない

---

## リファクタリング前後の比較テスト

以下を AI に依頼して確認スクリプトを作成:

> `theme/` ディレクトリ内の全 HTML / PHP ファイルを検索し、
> Hex カラーコード（#xxx, #xxxxxx）、px 直指定のフォントサイズ、
> フォントファミリー直書きを検出するシェルスクリプトを
> `scripts/check-hardcoded-values.sh` として生成してください。

---

## 次のステップ

`07_review_check.md` で最終的な品質チェック・バリデーションを行う。