# Windows10 home またはWindows8でのDocker  

Windows10 home またはWindows8以下ではHyper-Vが動かないので、[Docker Toolbox](https://www.docker.com/products/docker-toolbox)を使う。  
[Docker for Windows](docker_for_windows.md)との同居はでない。  
**ただし、ログインユーザ名は必ず半角アルファベットのみで作ること**  
すでに漢字でユーザ名を作ってしまった場合は、[WINDOWS8 で出来てしまった日本語名ユーザフォルダをリネームした話](http://dp-lab.org/blog/?p=470)など参考にして変更してください。

* [chocolatey](https://chocolatey.org/install)でのインストールが簡単。  
  手順は以下の通り。  
  VirtualBoxが入っていなければそれも入れておく。  
  ```
  choco install docker-toolbox -y
  choco install docker-kitematic -y
  ```
* ではありますが、本家からとってくる場合は以下からどうぞ。
  https://www.docker.com/products/docker-toolbox
* 「Docker Quickstart Terminal」を実行する。  
  ![Docker Quickstart Terminal](image/terminal.png)
