# セットアップ手順

Mac mini Home Server の詳細なセットアップ手順。

## 0. 設定アシスタント

### 0-1 管理者アカウントの作成
macOS設定アシスタントに沿って管理者アカウントを作成

### 0-2 入力機器の設定

#### トラックパッド
（システム設定 → トラックパッド から設定）

**ポイントとクリック**
- 追跡速度：速い（最速）に変更
- クリックの強さ：強め（最も強い）に変更
- タップでクリック：オン（1本指でタップ）に変更

**アクセシビリティ**
（システム設定 → アクセシビリティ → ポインタコントロール → トラックパッドオプション から設定）
- ドラッグにトラックパッドを使用：オンに変更
-　ドラッグ方法：3本指のドラッグに変更


#### キーボード
（システム設定 → キーボード から設定）
- キーのリピート速度：速い（最速）に変更
- リピート入力認識までの時間：短い（最速）に変更


## 1. Homebrewのインストール
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

## 2. gitのインストール
```bash
brew install git
```

## 3. このリポジトリをクローン
```bash
git clone https://github.com/shuheif0619/mac-mini-home-server.git

cd mac-mini-home-server
```

## 4. GitHubのリポジトリからBrewfileを実行

### 4-1 Brewfile.server
```bash
brew bundle install --file=Brewfile.server
```

### 4-2 Brewfile.dev
```bash
brew bundle install --file=Brewfile.dev
```


## 5. 標準アカウント作成 ＆ Appleアカウントログイン

### 5-1 標準アカウントの作成（ターミナルから）

```bash
sudo sysadminctl -addUser shuhei -fullName "Shuhei" -password -
```

コマンド実行後、以下の順で入力を求められる。
1. 管理者パスワード（`sudo`のため）
2. 対象ユーザーの新しいパスワード（画面には表示されない）


#### 作成確認
```bash
dscl . -list /Users | grep -E "shuhei"
```
必要な人数分のユーザー名が表示されれば成功。

> **Note:** アカウント作成後、ログイン後でパスワードが弾かれたため`-resetPasswordFor`によるパスワード変更を実施
> `Operation is not permitted without secure token unlock.`エラーが発生。
> Secure Tokenの制約により、コマンドラインからの変更ができなかった。
> **解決策：** システム設定 → ユーザとグループ から、GUI経由でパスワードを再設定して解決。
> コマンドラインでのSecure Token制約に当たった場合は、GUIでの操作に切り替えるのが確実。


### 5-2 各標準アカウントでAppleアカウントにサインイン

各アカウントに実際にログインし、以下の手順でサインインする。
1. 対象アカウントに切り替えてログイン
2. システム設定 → 画面上部のApple IDバナーから「サインイン」
3. 各自のApple IDとパスワードを入力

> 管理者アカウントはApple IDサインインをスキップ（サーバー管理専用のため個人情報と紐付けない方針）。
> 標準アカウント側は各自の個人Apple IDでサインインする。
