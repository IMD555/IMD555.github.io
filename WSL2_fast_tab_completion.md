Tab補完とかがすごい遅いのを何とかする。

1. Windows側の実行ファイルを探さないようにする。

```
sudo -i
echo -e "[interop]\nappendWindowsPath = false" >> /etc/wsl.conf
exit
```

2. VS Codeとexplorerとメモ帳だけは開けるようにする。

```
win_user=user

echo alias code=\"\\\"/mnt/c/Users/($win_user)/AppData/Local/Programs/Microsoft VS Code/bin/code\\\"\" >> ~/.bashrc

echo alias notepad=/mnt/c/Windows/notepad.exe >> ~/.bashrc

function explorer(){
dir=$(wslpath -w "$@")
#echo $dir
/mnt/c/Windows/explorer.exe $dir
}

#ついでに
alias ll='ls -alh'
alias dush='du -sh'
alias tar_comp='tar acvf'
alias tar_decomp='tar axvf'
```

(次のログインから有効)

参考:
https://4geek.net/wsl-tips/
https://qiita.com/nanagami1369/items/630f99813bf384a84d80

################################################################

以下試行錯誤のメモ

たぶん ↓ でもできる（どっちがいいんだろう？）

```
cd
mkdir win_exe
cd win_exe
echo "PATH=$PATH:$HOME/win_exe" >> .profile
ln -s /mnt/c/Users/<<<user>>>/AppData/Local/Programs/Microsoft\ VS\ Code/bin/code
ln -s /mnt/c/Windows/explorer.exe      # これはディレクトリ指定が効かない
ln -s /mnt/c/Windows/notepad.exe
cd
```




Windowsのバージョンによって？explorerだけ動かないことがある？(25352.1で確認)
aliasの代わりに.bashrcに↓を書くと動いた
```
function explorer(){
dir=$(wslpath -w "$@")
#echo $dir
/mnt/c/Windows/explorer.exe $dir
}
```

```
PATH+=:/mnt/c/Users/user/AppData/Local/Programs/Microsoft\ VS\ Code/bin
```
でもいいっぽいから、

```
cd
mkdir win_exe
cd win_exe
ln -s /mnt/c/Users/<<<user>>>/AppData/Local/Programs/Microsoft\ VS\ Code/bin/code
ln -s /mnt/c/Windows/explorer.exe
ln -s /mnt/c/Windows/notepad.exe
cd
echo "PATH+=:$HOME/win_exe" >> .profile
echo "PATH+=:/mnt/c/Users/user/AppData/Local/Programs/Microsoft\ VS\ Code/bin" >> .profile
```
