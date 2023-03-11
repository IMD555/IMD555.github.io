Tab補完とかがすごい遅いのを何とかする。

1. Windows側の実行ファイルを探さないようにする。

```
sudo -i
echo -e "[interop]\nappendWindowsPath = false" >> /etc/wsl.conf
exit
```

2. VS Codeとexplorerとメモ帳だけは開けるようにする。

\"\\\"path to exe including spaces\\\"\"

```
echo alias code=\"\\\"/mnt/c/Users/user/AppData/Local/Programs/Microsoft VS Code/bin/code\\\"\" >> ~/.bashrc
echo alias explorer=/mnt/c/Windows/explorer.exe >> ~/.bashrc
echo alias notepad=/mnt/c/Windows/notepad.exe >> ~/.bashrc
```
(次のログインから有効)

たぶん ↓ でもできる
```
cd
mkdir win_exe
cd win_exe
echo "PATH=$PATH:$HOME/win_exe" >> .profile
ln -s /mnt/c/Users/user/AppData/Local/Programs/Microsoft\ VS\ Code/bin/code
ln -s /mnt/c/Windows/explorer.exe
ln -s /mnt/c/Windows/notepad.exe
cd
```

参考:
https://4geek.net/wsl-tips/
https://qiita.com/nanagami1369/items/630f99813bf384a84d80
