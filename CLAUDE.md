# CLAUDE.md

> このファイルは Claude Code がプロジェクトの文脈を把握するための指示書です。
> 詳細なルールは `docs/prompts/00_rules.md` を参照してください。

## プロジェクト概要

WordPress ブロックテーマ（FSE）の開発プロジェクト。

## リポジトリ構成

```
repo/
  theme/                 # WP ブロックテーマ本体
  docker/                # Docker Compose 環境
  docs/
    prompts/             # AI 指示テンプレート（00〜07）
    decisions/           # ADR（Architecture Decision Records）
    figma/               # Figma エクスポート・スクショ
  scripts/               # 開発補助スクリプト
```

## 開発ワークフロー

1. `docs/prompts/` のプロンプトを番号順に実行する
2. 各ステップの成果物を確認してから次に進む
3. 設計判断は `docs/decisions/` に ADR として記録する

## コーディング規約（要約）

- WordPress Coding Standards 準拠
- **theme.json 優先**: スタイルは原則 theme.json で定義
- **コアブロック優先**: カスタムブロックは最終手段
- **functions.php 最小化**: 肥大化させない
- **命名**: kebab-case（ファイル）, snake_case + プレフィックス（PHP 関数）

## 重要なファイル

- `theme/theme.json` — デザインの Single Source of Truth
- `theme/functions.php` — 最小限に保つ
- `docs/prompts/00_rules.md` — 全ルールの詳細

## Docker 環境

```bash
# 起動
scripts/dev-up.sh

# 停止
scripts/dev-down.sh

# リセット（データ削除）
scripts/dev-reset.sh
```

- WordPress: http://localhost:8080
- phpMyAdmin: http://localhost:8081

## 指示を受ける際のルール

1. 変更対象のファイルパスと現在の内容がコンテキストにあることを確認する
2. theme.json のプリセット値を可能な限り使用する
3. ブロックマークアップの開始・終了タグの一致に注意する
4. 出力はファイルパスを明示して完全なコードを返す
5. 1 タスク 1 レスポンスを心がける