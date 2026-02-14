# 05 — ブロックパターン生成

> **前提**: `00_rules.md` 読み込み済み、テンプレートが動作していること
> **成果物**: Figma の各セクションに対応するブロックパターン（PHP ファイル）

---

## このプロンプトの目的

テンプレートに直書きしたセクションを**再利用可能なブロックパターン**として切り出す。
ユーザーがサイトエディタやブロックエディタからパターンを挿入できるようにする。

---

## パターンとは

WordPress のブロックパターンは、事前に定義したブロックの組み合わせ。
`theme/patterns/` に PHP ファイルとして配置すると、自動的に登録される（WP 6.0+）。

### ファイル形式

```php
<?php
/**
 * Title: ヒーローセクション
 * Slug: theme-slug/hero-section
 * Categories: featured, banner
 * Keywords: hero, banner, cover
 * Description: メインビジュアルと見出し・CTAボタンを含むヒーローセクション
 * Viewport Width: 1200
 */
?>
<!-- wp:cover {"...属性..."} -->
  <!-- ブロックマークアップ -->
<!-- /wp:cover -->
```

---

## 事前準備（人間がやること）

1. Figma の各セクションのスクリーンショットを `docs/figma/` に保存
2. パターン化するセクションのリストを作成（下記の表を埋める）
3. 現在のテンプレートから対象セクションの HTML を確認

---

## パターン一覧（ここを埋める）

| # | パターン名 | ファイル名 | カテゴリ | 説明 |
|---|---|---|---|---|
| 1 | *(例: ヒーロー)* | `hero-section.php` | `featured` | *(例: フルワイド背景画像+見出し+CTA)* |
| 2 | | | | |
| 3 | | | | |
| 4 | | | | |
| 5 | | | | |

---

## AI への指示（パターンごとに繰り返す）

### 指示フォーマット

```markdown
## 生成するパターン
- ファイルパス: `theme/patterns/{ファイル名}.php`
- パターン名: {日本語名}
- スラッグ: {テーマスラッグ}/{パターンスラッグ}
- カテゴリ: {featured / text / gallery / call-to-action / etc.}

## Figma デザイン
(スクリーンショットを添付)

## デザインの説明
- レイアウト: {フルワイド / コンテンツ幅}
- 背景: {色名 or 画像}
- 含まれる要素:
  - {見出し: H2, 白色, xx-large}
  - {段落テキスト: medium, muted}
  - {ボタン: primary カラー}
  - ...

## 使用する theme.json プリセット
- カラー: `var(--wp--preset--color--primary)` 等
- フォントサイズ: `var(--wp--preset--font-size--xx-large)` 等
- スペーシング: `var(--wp--preset--spacing--50)` 等

## 要件
- コアブロックのみで構成
- 翻訳可能な文字列は `esc_html__('テキスト', 'テーマスラッグ')` でラップ
- テーマ画像は `<?php echo esc_url( get_theme_file_uri( 'assets/images/xxx.jpg' ) ); ?>` を使用
- インラインスタイルは最小限に。theme.json プリセットで対応できるものはプリセットを使う
- プレースホルダーテキストは実際に使いそうな文言で

## 出力
- 完全な PHP ファイルとして出力してください
- ファイル先頭のコメントヘッダーを必ず含めてください
```

---

## よく使うパターンの構成例

### ヒーローセクション
```
wp:cover (フルワイド, 背景画像 or 背景色)
  └─ wp:group (コンテンツ幅, 中央揃え)
     ├─ wp:heading (H1)
     ├─ wp:paragraph (リード文)
     └─ wp:buttons
        └─ wp:button (CTA)
```

### 特徴セクション（3カラム）
```
wp:group (フルワイド, 背景色)
  └─ wp:group (コンテンツ幅)
     ├─ wp:heading (H2, セクションタイトル)
     └─ wp:columns (3カラム)
        ├─ wp:column
        │   ├─ wp:image (アイコン)
        │   ├─ wp:heading (H3)
        │   └─ wp:paragraph
        ├─ wp:column (同上)
        └─ wp:column (同上)
```

### CTA バナー
```
wp:group (フルワイド, アクセントカラー背景)
  └─ wp:group (コンテンツ幅, テキスト中央)
     ├─ wp:heading (H2)
     ├─ wp:paragraph
     └─ wp:buttons (中央揃え)
        └─ wp:button
```

### お問い合わせ（2カラム）
```
wp:group (フルワイド)
  └─ wp:columns (コンテンツ幅)
     ├─ wp:column (テキスト情報)
     │   ├─ wp:heading
     │   ├─ wp:paragraph (住所・電話等)
     └─ wp:column (フォーム)
        └─ wp:shortcode or wp:html (フォームプラグイン)
```

---

## テンプレートへの組み込み

パターンが完成したら、テンプレートを書き換えて `wp:pattern` で呼び出す。

```html
<!-- 変更前: ブロック直書き -->
<!-- wp:cover {"...省略..."} -->
  <!-- 長いブロックマークアップ -->
<!-- /wp:cover -->

<!-- 変更後: パターン参照 -->
<!-- wp:pattern {"slug":"theme-slug/hero-section"} /-->
```

---

## パターンカテゴリの登録

`functions.php` にカスタムカテゴリを追加する場合:

```php
function mytheme_register_pattern_categories() {
    register_block_pattern_category( 'mytheme-sections', [
        'label' => __( 'テーマセクション', 'theme-slug' ),
    ] );
}
add_action( 'init', 'mytheme_register_pattern_categories' );
```

---

## 確認ポイント

- [ ] パターンが「外観 → エディター → パターン」に表示される
- [ ] パターンをブロックエディタから挿入できる
- [ ] パターン内のカラー・フォントが `theme.json` プリセットを参照している
- [ ] 翻訳関数が正しく使われている
- [ ] テンプレートを `wp:pattern` 呼び出しに書き換えた後もレイアウトが同じ
- [ ] モバイル〜デスクトップでレスポンシブ表示が問題ない

---

## 次のステップ

`06_refactor_to_tokens.md` で全体を見直し、ハードコード値を `theme.json` トークンに統一する。