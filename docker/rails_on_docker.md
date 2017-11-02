# DockerによるRails環境の構築

## postos3を手動で構築する手順

centos7のイメージを使って、postos3devというコンテナを作り、コマンドラインを起動する。  
```
docker run -i -d -p 3000:3000 --name "postos3batch" -t centos:7 /sbin/init
docker exec -i -t postos3batch bash
```

|スイッチ|説明|
|---|---|
|-d|バックグラウンドで実行する|
|-p|ポートを開ける|
|-i|起動時にSTDOUTを開きっぱなし|
|-t|イメージ名称の指定|

そのまま起動するので、環境セットアップをメモしながら実行する。
