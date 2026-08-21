# Mac mini Home Server

## 概要
Mac mini (M4 Pro 14 Core CPU 20 Core GPU/ 48GB RAM / 2TB SSD / 10Gbps Ethernet) を、
家族3人で共有しつつ個人利用向けのホームサーバーとして構築するプロジェクト。

## 目的
- 自宅からのAIローカル推論・リモートアクセス環境の構築
- 家族向け写真共有基盤の構築（ハンドメイドアクセサリ販売業の商品写真管理を含む）
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

## ステータス
🚧 構築中（Mac mini到着: 2026年8月予定）
