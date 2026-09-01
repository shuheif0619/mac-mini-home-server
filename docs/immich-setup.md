# Immich セットアップ手順

Mac mini上に、Docker ComposeでImmichを構築する手順。

## 前提条件

- Docker Desktop インストール済み
- 外付けHDD（exFAT、`/Volumes/my passport`）がMac miniに接続済み
- Tailscale導入済み（外部アクセス用）
- **管理者アカウント（yorkies）で作業する**（サーバー関連の設定ファイルは管理者アカウント配下に集約する方針のため）

## 手順

### 1. 作業用フォルダの作成

管理者アカウントのホームディレクトリ配下に作成する。

```bash
mkdir ~/immich-app
cd ~/immich-app
```

（実際のパスは `/Users/yorkies/immich-app` になる）

### 2. 公式のdocker-compose.ymlと.envをダウンロード

```bash
wget -O docker-compose.yml https://github.com/immich-app/immich/releases/latest/download/docker-compose.yml
wget -O .env https://github.com/immich-app/immich/releases/latest/download/example.env
```

### 3. `.env` を編集

```bash
nano .env
```

主な変更点：

保存：`Ctrl+O` → Enter → `Ctrl+X`

### 4. アップロード先フォルダを事前に作成

```bash
mkdir -p "/Volumes/my passport/写真/immich-personal"
```

### 5. Immichを起動

```bash
docker compose up -d
```

> **注意**：コマンドは `docker compose`（スペース区切り）。`docker-compose`（ハイフン）は非推奨・エラーの原因になることがある。

### 6. 起動ログの確認

```bash
docker compose logs -f
```

（`Ctrl+C` で確認画面を終了）

### 7. ブラウザでアクセス・初期セットアップ

- Mac mini本体から：`http://localhost:2283`
- 別端末から（Tailscale経由）：`http://100.92.140.104:2283`

初回アクセス時に表示される登録画面で作成したアカウントが管理者になる。

### 8. スマートフォンに公式アプリを導入

- App StoreでImmichアプリをインストール
- サーバーURLとして `http://100.92.140.104:2283` を指定（Tailscale経由）し、作成したアカウントでログイン
- バックグラウンド自動アップロードを有効化
