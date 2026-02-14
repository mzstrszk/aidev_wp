# 02 — ブロックテーマ スキャフォールディング

> **前提**: `00_rules.md` を読み込み済み、Docker 環境が動作していること
> **成果物**: ブロックテーマとして WordPress に認識される最小限のファイル一式

---

## このプロンプトの目的

WordPress がブロックテーマとして認識する**最小構成**を生成する。
この段階ではデザインを作り込まず、骨格だけを確立する。

---

## AI への指示

以下の仕様でブロックテーマの基本ファイルを生成してください。

### テーマ情報

| 項目 | 値 |
|---|---|
| テーマ名 | *(ここに記入)* |
| テーマスラッグ | *(ここに記入: 例 `my-theme`)* |
| 説明 | *(ここに記入)* |
| 作者名 | *(ここに記入)* |
| テキストドメイン | *(テーマスラッグと同じ)* |
| WordPress 最低バージョン | 6.0 |
| PHP 最低バージョン | 8.0 |

### 生成するファイル

```
theme/
  style.css                  # テーマ宣言ヘッダーのみ
  theme.json                 # 最小限のグローバル設定
  functions.php              # 最小限の初期化
  templates/
    index.html               # フォールバックテンプレート
    single.html              # 投稿詳細
    page.html                # 固定ページ
    archive.html             # アーカイブ
    search.html              # 検索結果
    404.html                 # 404 エラー
    home.html                # ブログホーム（任意）
    front-page.html          # フロントページ（任意）
  parts/
    header.html              # サイトヘッダー
    footer.html              # サイトフッター
  patterns/
    (この段階では空でよい)
  assets/
    css/
    js/
    fonts/
    images/
      (screenshot.png 用の空ディレクトリ)
```

### 各ファイルの要件

#### `style.css`
- テーマ宣言ヘッダーのみ記述（実スタイルは書かない）
- `Requires at least`, `Tested up to`, `Requires PHP` を含める

#### `theme.json`
- `$schema` を指定
- `version: 3`（WordPress 6.6 以降対応。6.5 以前なら `2`）
- `settings` に最低限の `layout`（contentSize, wideSize）を定義
- `appearanceTools: true` を有効化
- カラーパレット・フォントはプレースホルダー（次ステップで Figma から流し込む）

#### `functions.php`
- テーマサポート登録（`after_setup_theme`）
- スタイルシート enqueue（`wp_enqueue_scripts`）
- パターンカテゴリ登録（`init`）
- **それ以上のコードは書かない**

#### テンプレート（`templates/*.html`）
- 全テンプレートで `header` / `footer` パーツを読み込む
- `index.html`: クエリループで投稿一覧を表示
- `single.html`: 投稿タイトル + コンテンツ + コメント
- `page.html`: ページタイトル + コンテンツ
- `archive.html`: アーカイブタイトル + クエリループ
- `search.html`: 検索フォーム + クエリループ
- `404.html`: 404 メッセージ + 検索フォーム

#### テンプレートパーツ（`parts/*.html`）
- `header.html`: サイトタイトル + ナビゲーション
- `footer.html`: コピーライト + サイト情報

### 出力形式

- ファイルごとに**ファイルパス**を明示して、**完全なコード**を出力してください
- `<!-- wp:xxx -->` ブロックマークアップの閉じタグに注意してください

---

## 確認ポイント

- [ ] 管理画面 →「外観 → テーマ」にテーマが表示される
- [ ] テーマを有効化してフロントエンドにアクセスできる
- [ ] 「外観 → エディター（サイトエディタ）」が開ける
- [ ] サイトエディタでテンプレート一覧が表示される
- [ ] サイトエディタでテンプレートパーツ一覧が表示される
- [ ] `theme.json` が JSON として valid（構文エラーなし）

---

## 次のステップ

`03_theme_json_from_figma.md` で Figma のデザイントークンを `theme.json` に流し込む。