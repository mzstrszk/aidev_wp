# 00 — プロジェクト共通ルール

> **用途**: このファイルの内容を `CLAUDE.md`（Claude Code 用）および `.cursorrules`（Cursor 用）に転記・参照させる。
> すべてのプロンプトはこのルールを前提として動作する。

---

## 1. プロジェクト概要

| 項目 | 値 |
|---|---|
| プロジェクト名 | *(ここにテーマ名を記入)* |
| テーマ種別 | WordPress ブロックテーマ（FSE: Full Site Editing） |
| WordPress バージョン | 6.x（Docker 環境） |
| PHP バージョン | 8.2 以上 |
| 対象ブラウザ | モダンブラウザ最新2バージョン |

## 2. リポジトリ構成

```
repo/
  theme/                 # WP ブロックテーマ本体
  docker/                # compose, nginx/apache, php, db
  docs/
    prompts/             # AI 指示テンプレート（本ファイル群）
    decisions/           # 仕様決定ログ（ADR）
    figma/               # エクスポート物、スクショ、リンク集
  scripts/               # 生成・検証・整形スクリプト
```

## 3. テーマディレクトリ構造

```
theme/
  style.css              # テーマ宣言（メタ情報のみ）
  theme.json             # グローバル設定・スタイル ★ Single Source of Truth
  functions.php          # 最小限（enqueue, パターン登録, 翻訳等）
  templates/             # ページテンプレート（HTML）
  parts/                 # テンプレートパーツ（header, footer 等）
  patterns/              # ブロックパターン（PHP）
  assets/
    css/                 # 追加スタイルシート（必要最小限）
    js/                  # フロントエンド JS
    fonts/               # Web フォント
    images/              # テーマ画像
```

## 4. コーディング規約

### 4.1 全般
- **WordPress Coding Standards** に準拠する
- インデントはタブ（PHP）/ 半角スペース 2 つ（HTML テンプレート, JSON）
- ファイルの末尾に改行を 1 つ入れる
- UTF-8（BOM なし）

### 4.2 theme.json 優先原則
- **スタイルは原則 `theme.json` で定義する**
- `theme.json` で対応できない場合のみ `assets/css/` に追加 CSS を置く
- `!important` の使用は禁止（やむを得ない場合はコメントで理由を記載）

### 4.3 テンプレート（HTML）
- WordPress ブロックマークアップ（`<!-- wp:xxx -->`）で構成する
- **コアブロックを最大限活用**し、カスタムブロックは最終手段とする
- テンプレート内にインライン CSS を書かない

### 4.4 パターン（PHP）
- ファイル先頭にパターンメタ情報のコメントヘッダーを記載する
- 翻訳対象の文字列は `esc_html__()` / `esc_attr__()` でラップする
- 画像パスは `get_theme_file_uri()` を使用する

### 4.5 functions.php
- **最小限に保つ**（肥大化させない）
- 機能追加が大きくなる場合は `inc/` にファイル分割する
- `after_setup_theme` でテーマサポートを登録
- `wp_enqueue_scripts` でスタイル・スクリプトを登録

## 5. 命名規則

| 対象 | 規則 | 例 |
|---|---|---|
| テンプレート | kebab-case | `single-post.html` |
| パーツ | kebab-case | `header.html`, `footer.html` |
| パターン | kebab-case | `hero-section.php`, `cta-banner.php` |
| CSS カスタムプロパティ | `--wp--custom--` プレフィックス | `--wp--custom--spacing--gutter` |
| PHP 関数 | snake_case + テーマプレフィックス | `mytheme_enqueue_assets()` |
| JS 関数 | camelCase | `initMobileMenu()` |

## 6. Git 運用

- ブランチ: `main`（安定版）/ `develop`（開発）/ `feature/*`（機能別）
- コミットメッセージ: Conventional Commits 形式
  - `feat:` / `fix:` / `docs:` / `style:` / `refactor:` / `chore:`
- `.gitignore` に WordPress コア、`node_modules`、`.env` を含める

## 7. AI への指示の出し方（共通ガイドライン）

1. **コンテキストを必ず渡す**: 変更対象のファイルパスと現在の内容を明示する
2. **1 プロンプト 1 タスク**: 複数の作業を混ぜない
3. **出力形式を指定する**: 「〇〇.php として出力してください」等
4. **theme.json の該当箇所を引用する**: スタイル変更時は必ず現在の設定値を伝える
5. **スクリーンショットを添付する**: デザイン再現の指示には Figma のスクショを使う

## 8. 品質基準

- [ ] Theme Check プラグインでエラーが 0 件
- [ ] Lighthouse アクセシビリティスコア 90 以上
- [ ] サイトエディタ上でテンプレート・パターンが正しく表示される
- [ ] モバイル（375px）〜デスクトップ（1440px）でレイアウト崩れがない
- [ ] `theme.json` が JSON として valid である