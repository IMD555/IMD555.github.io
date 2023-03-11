Tab補完とかがすごい遅いのを何とかする。

1. Windows側の実行ファイルを探さないようにする。

```
cd
sudo -i
echo "[interop]\nappendWindowsPath = false" >> /etc/wsl.conf
exit
```

1. VS Codeとexplorerとメモ帳だけは開けるようにする。

```
mkdir win_exe
cd win_exe
echo "PATH=$PATH:$HOME/win_exe" >> .profile
ln -s /mnt/c/Users/user/AppData/Local/Programs/Microsoft\ VS\ Code/bin/code
ln -s /mnt/c/Windows/explorer.exe
ln -s /mnt/c/Windows/notepad.exe
cd
```

# https://4geek.net/wsl-tips/
