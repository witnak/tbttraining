# DockerによるRails環境の構築

Dockerを使ってRailsの開発環境を作りたい。  

## Too long; Didn’t read

redmineを題材にして、Railsの開発環境をDcokerで構築する。  
お急ぎの方はこちらから。  

まず、Dockerをインストールする。  
* [Windows10 Pro 以上でのDockerインストール](docker_for_windows.md)
* [Windows10 home または Windows8 以下でのDockerインストール](docker_toolbox_for_win.md)
* [MacでのDockerインストール](docker_for_mac.md)

このリポジトリにある、[docker-compose.yml](docker-compose.yml)と[Dockerfile](Dockerfile)と[database.yml](database.yml)をひとつのフォルダにおく。  
そして、同じフォルダに、redmineをチェックアウトする。  
```
svn co https://svn.redmine.org/redmine/branches/3.4-stable/ ./redmine
```

ここでコンテナ起動。  
```
dcoker-compose up
```

Railsサーバにログイン（ほんとはちょっと違うけど）して、
```
docker exec -i -t railsdev bash
```

Redmineをセットアップ。
```
bundle install --without development test --path vendor/bundle
bundle exec rake generate_secret_token
bundle exec rake db:migrate
REDMINE_LANG=ja bundle exec rake redmine:load_default_data
```

Redmineサーバを起動する。  
（`-b`つけないと、host側からのアクセスに答えてくれない）  
```
bundle exec rails s -b '0.0.0.0'
```

これで、LinuxまたはMacならブラウザで http://localhost:3000/ を開く。
または、Windowsなら以下のコマンドで`dokcer-machine`でIPを確認して、ブラウザを開く。  
```
$ docker-machine ip default
192.168.99.100
```
ならば、http://192.168.99.100:3000/ となる。

## Railsが動作する環境を手動で構築した時の試行錯誤

目標は、
* redmineをdevelopmentモードで実行すること。  
* redmineはホスト側で編集閲覧できること。
  （redmineそのものが目的ではなく、開発環境構築の演習がしたい）

まずは試しに、centos7のイメージを使って、railsdevというコンテナを作り、コマンドラインを起動する。  
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

さて、この辺でちょっと立ち止まって考える。  
ふつう、マシン１台用意したりVirtualBoxなどのホスト型仮想環境を使う場合、ひとつのCentosマシンを用意し、railsもDBもそこに立ててしまって、すべて一つにまとめてしまう。  
本番環境でもなければ、それで十分だからだ。  
でもたぶん、Dockerはそういうスタイルではない。  
Dockerはおそらく、１コンテナで１サービスが正しいはずだ。  

ではどうするか。  

ひとまず、上記までの作業をまとめてDockerfileを作成する。  
Dockerfileは[こんな感じ](Dockerfile)で作った。  
Dockerfileができたら、いったん途中まで作ったコンテナを破棄する。  
```
# 止める
docker stop railsdev  
# 破棄する
docket rm railsdev
```

`railsdevimg`というイメージ名でbuildする。
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
動くのはとりあえず確認した。

いったん止める。  
```
docker stop railsdev
```
止めたコンテナを削除しておく。あとで作り直したいから。  
```
docker rm railsdev
```

redmineのコードを、 **ホスト** 側にダウンロードしておく。  
```
svn co https://svn.redmine.org/redmine/branches/3.4-stable/ ./redmine
```

redmineを取ってきたら、コンテナを起動。  
```
docker run --privileged -d -p 3000:3000 -v "$PWD/redmine:/var/lib/redmine" --name railsdev -t railsdevimg
```
さっきまでと違うのはこれがついたこと。  

|スイッチ|説明|
|---|---|
|-v|ボリュームをホストと接続する。"ホスト側：コンテナ側"|

ホスト側はフルパスじゃないとダメらしい。  

これで、`-v`で指定したフォルダが、あたかもファイル共有されているかの状態が出来ているはず。  

次の試みのため、今動いているコンテナは止めて削除しておく。  
```
docker stop railsdev
docker rmn railsdev
```

## dokcer-composeを使う。

複数のコンテナを一気に取り扱う仕組みが、docker-compose。  
DB側はたいしてやることはないはずなので、出来合いのものをほぼそのまま使おうと思う。
ひとまず[docker-compose.yml](docker-compose.yml)をこんな感じで作ってみた。  

この[docker-compose.yml](docker-compose.yml)のポイントは、`named volume`を作っているところ。  
```
#永続化のためのボリュームを作成
volumes:
  mariadb:
  vendor:
```

LinuxやMacの場合は、`valumes`タグに直接ホスト側のディレクトリを指定すると上手く繋げてくれるが、Windowsの場合は、アプリケーションが直接触るようなディレクトリは`named volume`を作ってやらないと上手くいかない。  

[docker-compose.yml](docker-compose.yml)と同じフォルダで、以下を実行。  
```
docker-compose up -d
```
`-d`を入れているので、バックグランドで動いている。  

```
docker exec -i -t railsdev bash
```

Redmineをインストールする。
```
bundle install --without development test --path vendor/bundle
bundle exec rake generate_secret_token
bundle exec rake db:migrate
REDMINE_LANG=ja bundle exec rake redmine:load_default_data
```

テーブル内容をちゃんと作成できたかは、mariadb(mysql)にログインして確認してみる。  
```
mysql -u root -p -h db
```

webrick起動するときは以下のようにする。
（`-b`つけないと、host側からのアクセスに答えてくれない）  
```
bundle exec rails s -b '0.0.0.0'
```

これでlocalhostないしは仮想マシンのipで3000ポートにアクセスするとredmineが動いているはず。
仮想マシンのIPを確認するには、以下。
```
dcoker-machine ip default
```


## １日の仕事が終わって終了する場合。

Windowsの場合は、docker-machineを止めてやらないと、シャットダウンができない。  
```
docker-compose down
docker-machine stop
```
