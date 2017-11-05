# DockerによるRails環境の構築

Dockerを使ってRailsの開発環境を作りたい。  

## Railsが動作する環境を手動で構築する手順

centos7のイメージを使って、railsdevというコンテナを作り、コマンドラインを起動する。  
目標は、redmineをdevelopmentモードで実行すること。  
```
docker run --privileged -d -p 3000:3000 --name "railsdev" -t centos:7 /sbin/init
```

|スイッチ|説明|
|---|---|
|--privileged|特権モードで動かす|
|-d|バックグラウンドで実行する|
|-p|ポートを開ける（ホスト側:コンテナ側）|
|-t|イメージ名称の指定|
|--name|コンテナに名前をつける（省略すると適当につけやがる）|

コマンドの末尾に、`/sbin/init`とつけているのは、`systemd`も一緒に動かしたいから。  
`-d`をつけているので、バックグラウンドで動作している。  

で、稼働中のコンテナにアクセスしたい場合は以下。  
```
docker exec -i -t railsdev bash
```
|スイッチ|説明|
|---|---|
|-i|コンテナの標準入力を開く|
|-t|ttyデバイスを使う（runの時と意味が違う！）|

そのまま起動するので、環境セットアップをメモしながら実行する。
```
$ docker exec -i -t railsdev bash
[root@16bbcac08bd2 /]#    ## <- コンテナ内のbashが動いている
[root@16bbcac08bd2 /]#
```

おおむね以下のような作業をする。  
１行づつ実行し、うまくいったものはテキストファイルか何かにコピペして取っておく。  
```
# yum install
yum -y update
yum -y install gcc git rsync bzip2 tar openssl openssl-devel readline-devel  zlib-devel libffi-devel gdbm-devel tk tk-devel tcl tcl-devel patch gcc-c++ which sqlite-devel wget openssh-server file
yum -y install ImageMagick
yum -y install subversion

# rbenvのインストール
git clone https://github.com/sstephenson/rbenv.git /root/.rbenv
git clone https://github.com/sstephenson/ruby-build.git /root/.rbenv/plugins/ruby-build
/root/.rbenv/plugins/ruby-build/install.sh
echo 'export PATH=/root/.rbenv/shims:/root/.rbenv/bin:$PATH' >> /root/.bashrc
echo 'eval "$(rbenv init -)"' >> /root/.bashrc
source /root/.bashrc

# rubyのインストール(2.4.2)
export CONFIGURE_OPTS=--disable-install-doc
rbenv install 2.4.2
rbenv global 2.4.2
rbenv rehash
rbenv exec gem install bundler
rbenv rehash

# bundlerのインストール
gem install bundler --no-rdoc --no-ri

# mariaDBのインストール
yum -y install mariadb mariadb-server mysql-devel
```





Dockerfileができたら、いったん途中まで作ったコンテナを破棄する。  
```
# 止める
docker stop railsdev  
# 破棄する
docket rm railsdev
```

`railsdevimg`でbuildする。
```
docker build -t railsdevimg .
```
ここでの`-t`は、runの時と同じで、イメージ名を指定する。  
最後の`.`は、Dockerfileがあるディレクトリを示す。  

イメージができているか確認する。
```
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED              SIZE
railsdevimg         latest              b7344342b028        About a minute ago   1.12GB
centos              7                   d123f4e55e12        30 hours ago         197MB
```

ビルドが完了したら、同じ条件でコンテナを起動してみる。
```
docker run --privileged -d -p 3000:3000 --name railsdev -t railsdevimg
```
で、稼働中のコンテナにアクセスしたい場合は以下（さっきと同じ）。  
```
docker exec -i -t railsdev bash
```
