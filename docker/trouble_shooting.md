# トラブルシューティング

以下、おもいつくトラブルシューティングを書いておく

## `New state of 'nil' is invalid.`がでて、bashごと道ずれにしてこける

Windows8などで使っている場合に起こる現象。  
https://qiita.com/bfkbx960/items/b0e8692d5bfe8adb5d5a  
http://sparkling-software.club/pekublog/?p=1373  
Windows8以下の`Docker Quickstart Terminal`が、日本語に対応していないため。  

Docker Toolboxをインストールしてあれば、おそらく同時にGit Bashもインストールされているのでそちらを使用することで回避できる。

* Git Bashを起動してOptions>Text>Character setからUTF-8を設定する。
* そのうえで、Git Bash上で
  ```
  docker-machine ssh default
  ```
  でいつものクジラが出てくる。  
* あとは同じように使える。  

## PCをシャットダウンしようとしても、VertualBoxが止まってくれない。

`docker-machine`は手動で止めないといけない。  
一日の終わりには、以下の２コマンドを実行すること。  
```
docker-compose stop
docker-machine stop default
```

## 環境を作り直したい。

ぜんぶ更地にして、もう一回作り直したい。  

* コンテナを止める。
  ```
  docker-compose stop
  ```

* コンテナをすべて削除する。
  ```
  docker rm `docker ps -a -q`
  ```

* ボリュームをすべて削除する。
  ```
  docker volume rm `docker volume ls -q`
  ```

* コンテナのimageをビルドしなおし。
  ```
  docker-compose build
  ```

* もう一度コンテナを起動する。
  ```
  docker-compose up -d
  ```

* あとは、appコンテナに入ってセットアップ用の rake コマンドを実行しなおす。
