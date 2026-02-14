# 01 — Docker + WordPress 環境構築

> **前提**: `00_rules.md` を読み込み済みであること
> **成果物**: `docker/` 配下の設定ファイル一式、動作する WordPress 環境

---

## このプロンプトの目的

ローカル開発用の Docker 環境を構築し、テーマディレクトリが即時反映されるようにする。

---

## AI への指示

以下の要件で Docker 環境を構築してください。

### 構成

```
docker/
  docker-compose.yml
  .env.example          # 環境変数テンプレート
  config/
    php.ini             # PHP カスタム設定
    uploads.ini         # アップロードサイズ等
```

### 要件

1. **サービス構成**
   - `wordpress`: 公式イメージ `wordpress:php8.2-apache`（または nginx 構成）
   - `db`: `mysql:8.0` または `mariadb:10.11`
   - `phpmyadmin`: 開発時のデータ確認用（任意）

2. **ボリュームマウント**
   - `../theme` → コンテナ内 `/var/www/html/wp-content/themes/{テーマ名}`
   - ホスト側のファイル編集が即座にコンテナに反映されること

3. **ポート**
   - WordPress: `8080:80`
   - phpMyAdmin: `8081:80`

4. **環境変数（`.env.example`）**
   ```
   MYSQL_ROOT_PASSWORD=root_password
   MYSQL_DATABASE=wordpress
   MYSQL_USER=wp_user
   MYSQL_PASSWORD=wp_password
   WORDPRESS_DB_HOST=db
   WORDPRESS_DB_USER=wp_user
   WORDPRESS_DB_PASSWORD=wp_password
   WORDPRESS_DB_NAME=wordpress
   WORDPRESS_DEBUG=1
   WORDPRESS_TABLE_PREFIX=wp_
   ```

5. **PHP 設定**
   - `upload_max_filesize = 64M`
   - `post_max_size = 64M`
   - `memory_limit = 256M`
   - `display_errors = On`（開発用）

6. **起動・停止コマンドをスクリプト化**
   - `scripts/dev-up.sh`: `docker compose -f docker/docker-compose.yml up -d`
   - `scripts/dev-down.sh`: `docker compose -f docker/docker-compose.yml down`
   - `scripts/dev-reset.sh`: ボリューム削除込みの完全リセット

### 確認ポイント

- [ ] `docker compose up -d` で全サービスが起動する
- [ ] `http://localhost:8080` で WordPress 初期設定画面が表示される
- [ ] テーマディレクトリが管理画面「外観 → テーマ」に表示される
- [ ] `theme/` 内のファイル変更がブラウザリロードで反映される

---

## 補足: 初回セットアップ後の手順

1. WordPress 初期設定を完了する（日本語選択）
2. パーマリンク設定を「投稿名」に変更
3. テーマを有効化する
4. 必要に応じてダミーコンテンツをインポート（Theme Unit Test Data 等）

---

## トラブルシューティング用プロンプト（追加指示）

> Docker で WordPress を起動したところ、以下のエラーが出ました。
> ```
> （ここにエラーメッセージを貼る）
> ```
> 原因と修正方法を教えてください。