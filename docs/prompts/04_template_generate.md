# 04 — テンプレート生成・レイアウト構築

> **前提**: `00_rules.md` 読み込み済み、`theme.json` にデザイントークンが反映済み
> **成果物**: Figma のページレイアウトを再現したテンプレート HTML 群

---

## このプロンプトの目的

`02` で生成した骨格テンプレートを、Figma のレイアウトに合わせて**具体的な構造**に書き換える。
パターン（再利用コンポーネント）はこの段階では埋め込まず、テンプレートレベルの構造を確定させる。

---

## 事前準備（人間がやること）

1. Figma で各ページのレイアウトをスクリーンショットし、`docs/figma/` に保存
2. 各ページで使用するセクション（ヒーロー、CTA、一覧 等）をリストアップ
3. 現在の `theme.json` の内容を確認（AI に渡すため）

---

## テンプレートごとの指示テンプレート

以下をテンプレートごとにコピーし、情報を埋めて AI に指示する。

---

### 指示フォーマット

```markdown
## 対象テンプレート
- ファイルパス: `theme/templates/{テンプレート名}.html`

## 現在の theme.json のレイアウト設定
- contentSize: {値}
- wideSize: {値}

## Figma レイアウト（上から順に）
1. {セクション名}: {概要説明}
2. {セクション名}: {概要説明}
3. ...

## 添付
- Figma スクリーンショット: (画像を添付)

## 要件
- テンプレートパーツ `header` / `footer` を先頭・末尾に配置
- コアブロックのみで構成する（カスタムブロック不可）
- theme.json で定義済みのプリセット値（カラー、スペーシング、フォントサイズ）を使用
- `<!-- wp:pattern -->` は使わず、この段階ではブロックをベタ書きでよい
  （後で `05_pattern_generate.md` でパターン化する）
- レスポンシブ対応は WordPress のデフォルト挙動に任せる
  （カラム数の変更等が必要な場合は `style` 属性で指定）

## 出力
- 完全な HTML ファイルとして出力してください
```

---

## テンプレート別ガイドライン

### `front-page.html`（トップページ）

よく使うブロック構成:

```
<!-- wp:template-part {"slug":"header","area":"header"} /-->

<!-- wp:group {"align":"full","layout":{"type":"default"}} -->
  <!-- セクション群をここに -->
<!-- /wp:group -->

<!-- wp:template-part {"slug":"footer","area":"footer"} /-->
```

- ヒーローセクション: `wp:cover` または `wp:group` + 背景色
- 特徴セクション: `wp:columns` で 2〜4 カラム
- CTA: `wp:group` + `wp:buttons`
- 最新記事: `wp:query` ループ

### `single.html`（投稿詳細）

```
header
└─ group (contentWidth)
   ├─ post-title
   ├─ post-date / post-author / post-terms（メタ情報）
   ├─ post-featured-image
   ├─ post-content
   ├─ post-terms（タグ等）
   └─ comments / post-comments-form
footer
```

### `page.html`（固定ページ）

```
header
└─ group (contentWidth)
   ├─ post-title
   ├─ post-featured-image（任意）
   └─ post-content
footer
```

### `archive.html`（アーカイブ）

```
header
└─ group (contentWidth)
   ├─ query-title
   ├─ term-description（任意）
   └─ query (loop)
      ├─ post-template
      │   ├─ post-featured-image
      │   ├─ post-title (リンク付き)
      │   ├─ post-excerpt
      │   └─ post-date
      ├─ query-pagination
      └─ query-no-results
footer
```

### `search.html`（検索結果）

```
header
└─ group (contentWidth)
   ├─ search（検索フォーム）
   ├─ query-title
   └─ query (loop) — archive と同様
footer
```

### `404.html`

```
header
└─ group (contentWidth, テキスト中央揃え)
   ├─ heading "ページが見つかりません"
   ├─ paragraph 説明文
   └─ search（検索フォーム）
footer
```

---

## 確認ポイント

- [ ] 各テンプレートがサイトエディタで正しく表示される
- [ ] フロントエンドで意図したレイアウトになっている
- [ ] コンテンツ幅・ワイド幅が `theme.json` の設定通り
- [ ] ブロックマークアップの構文エラーがない（閉じタグ不一致等）
- [ ] クエリループで投稿が表示される（ダミーデータが必要）
- [ ] ナビゲーション（header）が動作する

---

## 次のステップ

`05_pattern_generate.md` でテンプレート内の再利用セクションをパターンとして切り出す。