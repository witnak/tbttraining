# MacでのDockerのインストール  

* homebrewくらいは入れてるよね？  
  ```
  brew install docker
  brew cask install docker # GUIで確認する場合。
  ```

* 本家はこちら。  
  https://docs.docker.com/docker-for-mac/
  特に変わったことをするわけではないので、stableでいいと思う。  

* インストールができたかどうかの確認
  `docker version`コマンドでこんな感じの出力があれば、まあOK。
  ```
  $ docker version
  Client:
   Version:      17.09.0-ce
   API version:  1.32
   Go version:   go1.8.3
   Git commit:   afdb6d4
   Built:        Tue Sep 26 22:40:09 2017
   OS/Arch:      darwin/amd64

  Server:
   Version:      17.09.0-ce
   API version:  1.32 (minimum version 1.12)
   Go version:   go1.8.3
   Git commit:   afdb6d4
   Built:        Tue Sep 26 22:45:38 2017
   OS/Arch:      linux/amd64
   Experimental: true
  ```
