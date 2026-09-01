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
