# Mac mini Home Server

## 概要
Mac mini (M4 Pro 14 Core CPU 20 Core GPU/ 48GB RAM / 2TB SSD / 10Gbps Ethernet) を、
家族3人で共有しつつ個人利用向けのホームサーバーとして構築するプロジェクト。

## 目的
- 自宅からのAIローカル推論・リモートアクセス環境の構築
- 家族向け写真共有基盤の構築（ネット販売業の商品写真管理を含む）
- インフラ・ネットワーク・コンテナ運用の実践的な学習

## 構成
- **Tailscale**: 自分専用の外部アクセス（VPN）
- **Cloudflare Tunnel**: 家族向けサービスの限定公開
- **Docker**: 各サービスのコンテナ管理
- **Syncthing**: iPhoneとの写真自動同期
- **SMB共有**: 家族向けファイル共有

## ディレクトリ構成
- `Brewfile.server` - サーバー運用に必要なパッケージ
- `Brewfile.dev` - 開発用ツール（他端末でも再利用可能）
- `docs/` - セットアップ記録、トラブルシューティング
  - [`setup-notes.md`](docs/setup-notes.md) - 詳細なセットアップ手順
  - [`fliqlo-wall-clock.md`](docs/fliqlo-wall-clock.md) - Fliqlo壁掛け時計化の設定手順

## クイックスタート

### 1. Homebrewのインストール
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

### 2. gitのインストール
```bash
brew install git
```

### 3. このリポジトリをクローン
```bash
git clone https://github.com/shuheif0619/mac-mini-home-server.git
cd mac-mini-home-server
```

### 4. Brewfileからパッケージをインストール
```bash
# サーバー運用に必要なパッケージ
brew bundle install --file=Brewfile.server

# 開発用ツール
brew bundle install --file=Brewfile.dev
```

### 5. 詳細なセットアップ手順
詳細なセットアップ手順は [`docs/setup-notes.md`](docs/setup-notes.md) を参照してください。
