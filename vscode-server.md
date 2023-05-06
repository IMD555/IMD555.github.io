Visual Studio Code Serverとはなんぞや

https://code.visualstudio.com/docs/remote/vscode-server

できるようになること
- WEBブラウザやVS Codeから、サーバーを操作

特徴
- インターネット越しでもルーターの設定が不要
- サーバーでGUI不要
- 仮想マシンやコンテナも可

操作元を「PC」、操作先を「SV」と書き、それぞれ以下の状態を想定

- PC
  - インターネットに接続されている
  - WEBブラウザが使え、GitHubにログインしている
  - VS Code 1.74 以降がインストールされていて、GitHubにログインしている

- SV（サーバー）
  - インターネットに接続されている
  - 直接操作かSSHができる（初期設定のため）
  - LinuxなどのOS

# VS Code の拡張機能の追加

Ctrl+P を押して `ext install ms-vscode.remote-server` を実行

# Linux用のバイナリのダウンロード

アーキテクチャごとに違う

## x64

```
[user@SV:~$] wget "https://code.visualstudio.com/sha/download?build=stable&os=cli-alpine-x64" -O vscode-server.tar.gz
```

## ARM 32

```
[user@SV:~$] wget "https://code.visualstudio.com/sha/download?build=stable&os=cli-linux-armhf" -O vscode-server.tar.gz
```

## ARM 64

```
[user@SV:~$] wget "https://code.visualstudio.com/sha/download?build=stable&os=cli-alpine-arm64" -O vscode-server.tar.gz
```

# 解凍

```
[user@SV:~$] tar xf vscode-server.tar.gz
```

# 初期設定

```
[user@SV:~$] ./code tunnel
*
* Visual Studio Code Server
*
* By using the software, you agree to
* the Visual Studio Code Server License Terms (https://aka.ms/vscode-server-license) and
* the Microsoft Privacy Statement (https://privacy.microsoft.com/en-US/privacystatement).
*
? Do you accept the terms in the License Agreement (Y/n)? (y/n) › yes
To grant access to the server, please log into https://github.com/login/device and use code XXXX-XXXX
```

ライセンス合意の行は、vscodeからSSHなどした場合は表示されない。表示されたらEnterを押す。
PCで https://github.com/login/device にアクセスし、表示されたコード XXXX-XXXX を入力し、緑のボタンを2回押す。

サーバーに以下の行が表示されるので、希望の名前を入力する（自分のGitHubアカウントのデバイスでユニーク）。
入力しない場合は()の中の名前になる。

```
? What would you like to call this machine? (pensive-kite) › 
```

"sv"にした例

```
✔ What would you like to call this machine? · sv
[2023-02-23 03:18:44] info Creating tunnel with the name: sv

Open this link in your browser https://vscode.dev/tunnel/sv
```

この URL で WEB ブラウザからアクセスできる。
https://vscode.dev/tunnel/sv/home/user とかやると、/home/user を開ける。
お気に入り登録もおすすめ。

VS Codeでアクセスするには、左端の「リモートエクスプローラー」からTunnelsの一覧を見る。
初回は「サインインして登録済みのトンネルを表示する」
サーバーもメッセージも出ない場合、多分VS Codeが古い。

# 設定を吹き飛ばす

```
[user@SV:~$] rm -r .vscode*
```

# デーモン化する

`cat`の結果が同じになるように`nano`とかでファイル作成、編集する。
ユーザー名が`user`ではない場合は気を付ける。

```
[user@SV:~$] sudo nano /etc/systemd/system/vscode-server.service
[user@SV:~$] cat /etc/systemd/system/vscode-server.service
[Unit]
Description=VS Code Server Service

[Service]
User=user
ExecStart=/home/user/code tunnel

[Install]
WantedBy=multi-user.target

[user@SV:~$] sudo systemctl start vscode-server.service
[user@SV:~$] sudo systemctl enable vscode-server.service
[user@SV:~$] systemctl status vscode-server.service
● vscode-server.service - VS Code Server Service
     Loaded: loaded (/etc/systemd/system/vscode-server.service; enabled; preset: enabled)
     Active: active (running) since Sun 2023-05-07 02:12:14 JST; 22min ago
   Main PID: 628 (code)
      Tasks: 61 (limit: 1958)
        CPU: 4min 37.345s
     CGroup: /system.slice/vscode-server.service
             ├─ 628 /home/user/code tunnel
             ├─ 838 sh /home/user/.vscode-cli/servers/Stable-252e5463d60e63238250799aef7375787f68b>
             ├─ 842 /home/user/.vscode-cli/servers/Stable-252e5463d60e63238250799aef7375787f68b4ee>
             ├─ 853 /home/user/.vscode-cli/servers/Stable-252e5463d60e63238250799aef7375787f68b4ee>
             ├─ 998 /home/user/.vscode-cli/servers/Stable-252e5463d60e63238250799aef7375787f68b4ee>
             ├─1006 /home/user/.vscode-cli/servers/Stable-252e5463d60e63238250799aef7375787f68b4ee>
             ├─1072 /bin/bash --init-file /home/user/.vscode-cli/servers/Stable-252e5463d60e632382>
             ├─2153 systemctl status vscode-server.service
             └─2154 less

```

再起動してもつながることを確認する。

`/etc/systemd/system/vscode-server.service`を編集した後、反映させたい場合は再起動か
`sudo systemctl daemon-reload`。
