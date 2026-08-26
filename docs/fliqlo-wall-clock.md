# Mac mini + Studio Display 壁掛け時計化（Fliqlo）セットアップ手順

Mac mini と Studio Display を、Fliqlo スクリーンセーバーで壁掛け時計として運用するための設定手順まとめ。

## 目的

- 日中（6:30〜24:29）は Fliqlo のフリップクロック風スクリーンセーバーを常時表示し、壁掛け時計として使う
- 深夜（0:30〜6:29）は画面を完全オフにする

## 1. Fliqloのインストール

Homebrew経由でインストールし、全アカウント共通の場所にコピーする。

```bash
brew install --cask fliqlo
```

デフォルトではユーザー固有の場所にインストールされるため、全アカウント（yorkies / shuhei / arisa / atsuko）で使えるよう `/Library/Screen Savers/` にコピーする。

```bash
sudo cp -R ~/Library/Screen\ Savers/Fliqlo.saver /Library/Screen\ Savers/
```

## 2. 各アカウントでスクリーンセーバーを選択

コマンドラインでのモジュール指定は反映されないため、**各アカウントに実際にログインし、GUIで選択する**必要がある。

1. ファストユーザスイッチで対象アカウントに切り替え
2. システム設定 →「壁紙とスクリーンセーバー」（または「スクリーンセーバー」）
3. 一覧から Fliqlo を選択

これを4アカウント全てで実施。

## 3. 待機時間（アイドルタイム）の設定

各アカウントで、スクリーンセーバーが起動するまでの待機時間を10分（600秒）に設定。

```bash
defaults -currentHost write com.apple.screensaver idleTime 600
```

各アカウントにログインした状態で実行（または `sudo -u <ユーザー名>` で対象アカウントを指定して実行）。

## 4. ディスプレイスリープの無効化

`displaysleep` がアイドルタイム経由でスクリーンセーバーより先に画面を落としてしまわないよう、無効化しておく。

```bash
sudo pmset -c displaysleep 0
```

> **注意**：この設定は原因不明のタイミングで `10` に戻ってしまうことがあった。再発した場合、スクリーンセーバー起動後数秒で画面が真っ暗になる症状が出るため、`pmset -g` で `displaysleep` の値を確認すること。

## 5. 深夜〜早朝の画面オン/オフスケジュール

LaunchDaemon を使い、深夜0:30に画面オフ、朝6:30に画面オンとなるよう設定。

### 画面オフ（0:30）— `/Library/LaunchDaemons/com.user.displayoff.plist`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.user.displayoff</string>
    <key>ProgramArguments</key>
    <array>
        <string>/usr/bin/pmset</string>
        <string>displaysleepnow</string>
    </array>
    <key>StartCalendarInterval</key>
    <dict>
        <key>Hour</key>
        <integer>0</integer>
        <key>Minute</key>
        <integer>30</integer>
    </dict>
</dict>
</plist>
```

### 画面オン（6:30）— 当初の実装（※後述の問題あり）

`/Library/LaunchDaemons/com.user.displayon.plist` として、`caffeinate -u -t 3` を実行するLaunchDaemonを作成。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.user.displayon</string>
    <key>ProgramArguments</key>
    <array>
        <string>/usr/bin/caffeinate</string>
        <string>-u</string>
        <string>-t</string>
        <string>3</string>
    </array>
    <key>StartCalendarInterval</key>
    <dict>
        <key>Hour</key>
        <integer>6</integer>
        <key>Minute</key>
        <integer>30</integer>
    </dict>
</dict>
</plist>
```

読み込み：

```bash
sudo launchctl load -w /Library/LaunchDaemons/com.user.displayoff.plist
sudo launchctl load -w /Library/LaunchDaemons/com.user.displayon.plist
```

## 6. 発生した問題と原因

### 問題①：スクリーンセーバー起動直後に画面が真っ暗になる

- **原因**：`displaysleep` が `0` から `10` に戻ってしまっていた
- **対処**：`sudo pmset -c displaysleep 0` を再実行して解消

### 問題②：終日、時計（スクリーンセーバー）が一切表示されない

5時間以上の外出後、誰も操作していないのに一度も画面が表示されていなかった。

- **原因（推定）**：`caffeinate -u` は本来「画面を起こす」機能を持つが、これを **LaunchDaemon**（root権限・GUIセッションに非依存）から実行すると、正しいユーザーセッションのWindowServerに信号が届かず、画面が実際には起きない
- **対処（対応中）**：`com.user.displayon` を LaunchDaemon ではなく **LaunchAgent** として `/Library/LaunchAgents/` に再作成する。LaunchAgent はログイン中のGUIセッションに紐づいて実行されるため、`caffeinate -u` が正しく機能することを期待している

```bash
# 旧LaunchDaemonの削除
sudo launchctl unload /Library/LaunchDaemons/com.user.displayon.plist
sudo rm /Library/LaunchDaemons/com.user.displayon.plist

# 新しいLaunchAgentの作成（内容はdisplayonと同じ、置き場所のみ変更）
# → /Library/LaunchAgents/com.user.displayon.plist
```

権限設定：

```bash
sudo chown root:wheel /Library/LaunchAgents/com.user.displayon.plist
sudo chmod 644 /Library/LaunchAgents/com.user.displayon.plist
```

反映には、全アカウントのログアウト・再ログイン（またはMac mini再起動）が必要。

> **検証待ち**：翌朝6:30以降、Mac mini本体に触れずiPhoneのTermius（SSH）経由で確認予定。

## 7. 未解決の別問題：スリープ/スクリーンセーバー解除時のパスワード不一致

- ディスプレイスリープ解除時、およびホットコーナーからのスクリーンセーバー解除時に、正しいパスワードを入力しても弾かれる（画面が揺れる＝明確に「不一致」と判定されている）
- 全アカウントで発生。ファストユーザスイッチでの切り替え時は発生しない
- **発生時期**：アカウント作成直後から発生しており、今回のスクリーンセーバー設定より前からの問題と判明
- 原因調査中（キーボード配列の不一致、パスワード登録時の文字化けなどを疑い中）
