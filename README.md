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

## セットアップ手順

### 0. 設定アシスタント

#### 0-1 管理者アカウントの作成
macOS設定アシスタントに沿って管理者アカウントを作成

#### 0-2 入力機器の設定

##### トラックパッド
（システム設定 → トラックパッド から設定）

**ポイントとクリック**
- 追跡速度：速い（最速）に変更
- クリックの強さ：強め（最も強い）に変更
- タップでクリック：オン（1本指でタップ）に変更

**アクセシビリティ**
（システム設定 → アクセシビリティ → ポインタコントロール → トラックパッドオプション から設定）
- ドラッグにトラックパッドを使用：オンに変更
-　ドラッグ方法：3本指のドラッグに変更


##### キーボード
（システム設定 → キーボード から設定）
- キーのリピート速度：速い（最速）に変更
- リピート入力認識までの時間：短い（最速）に変更


### 1. Homebrewのインストール
\`\`\`bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
\`\`\`

### 2. gitのインストール
\`\`\`bash
brew install git
\`\`\`

### 3. このリポジトリをクローン
\`\`\`bash
git clone https://github.com/あなたのユーザー名/mac-mini-home-server.git
cd mac-mini-home-server
\`\`\`

### 4. GitHubのリポジトリからBrewfileを実行

#### 4-1 Brewfile.server
\`\`\`bash
brew bundle install --file=Brewfile.server
\`\`\`

#### 4-2 Brewfile.dev
\`\`\`bash
brew bundle install --file=Brewfile.dev
\`\`\`


### 5. 標準アカウント作成 ＆ Appleアカウントログイン

#### 5-1 標準アカウントの作成（ターミナルから）

\`\`\`bash
sudo sysadminctl -addUser shuhei -fullName "Shuhei" -password -
\`\`\`

コマンド実行後、以下の順で入力を求められる。
1. 管理者パスワード（`sudo`のため）
2. 対象ユーザーの新しいパスワード（画面には表示されない）


##### 作成確認
\`\`\`bash
dscl . -list /Users | grep -E "shuhei"
\`\`\`
必要な人数分のユーザー名が表示されれば成功。

> **Note:** アカウント作成後、ログイン後でパスワードが弾かれたため`-resetPasswordFor`によるパスワード変更を実施
> `Operation is not permitted without secure token unlock.`エラーが発生。
> Secure Tokenの制約により、コマンドラインからの変更ができなかった。
> **解決策：** システム設定 → ユーザとグループ から、GUI経由でパスワードを再設定して解決。
> コマンドラインでのSecure Token制約に当たった場合は、GUIでの操作に切り替えるのが確実。


#### 5-2 各標準アカウントでAppleアカウントにサインイン

各アカウントに実際にログインし、以下の手順でサインインする。
1. 対象アカウントに切り替えてログイン
2. システム設定 → 画面上部のApple IDバナーから「サインイン」
3. 各自のApple IDとパスワードを入力

> 管理者アカウントはApple IDサインインをスキップ（サーバー管理専用のため個人情報と紐付けない方針）。
> 標準アカウント側は各自の個人Apple IDでサインインする。
