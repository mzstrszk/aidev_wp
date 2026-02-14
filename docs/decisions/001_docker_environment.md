# ADR-001: Docker 環境構成

## ステータス

承認済み

## コンテキスト

ローカル開発環境として Docker を使用する。
WordPress の動作に必要な Web サーバー、PHP、データベースの構成を決める必要がある。
チームメンバーの環境差異を吸収し、再現性のある開発環境を提供したい。

## 決定

以下の構成を採用する。

- **Web サーバー + PHP**: `wordpress:php8.2-apache`（公式イメージ、Apache 同梱）
- **データベース**: `mysql:8.0`
- **管理ツール**: `phpmyadmin`（開発時のみ）
- **テーマディレクトリ**: ホスト側の `theme/` をコンテナにバインドマウント

## 選択肢

| 選択肢 | メリット | デメリット |
|---|---|---|
| A: wordpress:php8.2-apache + MySQL 8.0（採用） | 公式イメージで設定が少ない。WordPress との互換性が高い | Apache のカスタマイズ性が nginx より低い |
| B: nginx + php-fpm + MySQL 8.0 | パフォーマンスが高い。本番に近い構成が可能 | docker-compose が複雑になる。nginx.conf の管理が必要 |
| C: wordpress:php8.2-apache + MariaDB 10.11 | WordPress は公式に MariaDB をサポート。軽量 | MySQL 固有の機能を使う場合に差異が出る可能性 |
| D: Local（LocalWP）を使う | GUI で簡単。ワンクリック構築 | Docker ベースの他ツールと組み合わせにくい。チーム共有が難しい |

## 結果

- チームメンバーは `docker compose up -d` だけで環境を構築できる
- テーマディレクトリのバインドマウントにより、ホスト側の編集が即座に反映される
- 本番環境が nginx の場合、ステージングで別途確認が必要
- PHP バージョンを変更したい場合はイメージタグを変更するだけで対応可能

## 日付

YYYY-MM-DD