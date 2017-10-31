# CentOS7にRedmineをインストールする

## 1. SELinuxを無効にする
SELinuxのアクセス制限によってインストール作業や運用でつまづく可能性があるので無効にする。<br><br>
rootユーザーで /etc/sysconfig/selinux　を開き、SELINUX=　の値をdisabledに変更する。<br>
<img src=image/SELinux.JPG><br>
変更後は`reboot`コマンドを実行してCentOSを再起動<br>
再起動後、`getenforce`コマンドを実行してSELinuxが無効になったことを確認する。<br>
Disabledと表示されればSELinuxは無効になっている。

## 2. firewalldでHTTPを許可
CentOS7の初期状態ではFirewalld（ファイアウォール）が有効になっており、外部からサーバー上のHTTPにに接続することができない。クライアントのwebブラウザからアクセスできるようにFirewalldの設定を変更する<br>
<br>
rootユーザーで下記コマンドを実行
* **デフォルトzoneにhttpを追加する**<br>

  ```
  $ firewall-cmd --zone=public --add-service=http --permanent
  success
  ```
* **追加した設定を反映**<br>
  ```
  $ firewall-cmd --reload
  success
  ```
* **httpでのアクセスが許可されたか確認**
  ```
  $ firewall-cmd --zone=public --list-services
  dhcpv6-client http ssh
  ```

## 3. 必要なパッケージのインストール  
rootユーザーで下記コマンドを実行する。
* **開発ツールのインストール**  

  `$ yum -y groupinstall "開発ツール"`  

* **Passengerのビルドに必要なヘッダファイルなどのインストール**  

  `$ yum install curl-devel libyaml-devel libffi-devel`  

* **Postgreとヘッダファイルのインストール**  

  `$ yum -y install postgresql-server postgresql-devel`  

* **Apacheとヘッダファイルのインストール**  

  `$ yum -y install httpd httpd-devel`  

* **ImageMagickとヘッダファイル・日本語フォントのインストール**  

  `$ yum -y install ImageMagick ImageMagick-devel ipa-pgothic-fonts`  

  ※ImageMagickと日本語フォントはガントチャートをPNG形式の画像にエクスポートする機能、添付ファイルのサムネイル画像を作成するのに使われるため、インストールしなくてもRedmine実行は可能。

## 4. bundlerのインストール
Ruby用のパッケージ管理ツールであるbundlerをインストールする。  
Redmineが使用するgemパッケージをインストールする時に必要になる。  

`$ gem install bundler --no-rdoc --no-ri`  

## 5. PostgreSQLの設定
* **データベースクラスタの新規作成**  

  `$ postgresql-setup initdb`  

  データベースクラスタ・・・1つのサーバインスタンスによって管理されるデータベースの集合体  

* **RedmineからPostgreSQLに接続するための設定を追加**  

  rootユーザーで /var/lib/pgsql/data/pg_hba.conf　を開き、下の方にスクロールすると下記の記述があるので、  

  ```
  # Put your actual configuration here
  # ----------------------------------
  #
  # If you want to allow non-local connections, you need to add more
  # "host" records.  In that case you will also need to make PostgreSQL
  # listen on a non-local interface via the listen_addresses
  # configuration parameter, or via the -i or -h command line switches.
  ```
  そこに  

  ```
  host    redmine         redmine         127.0.0.1/32            md5
  host    redmine         redmine         ::1/128                 md5
  ```

  を追加記述して、このようにする。  

  ```
  # Put your actual configuration here
  # ----------------------------------
  #
  # If you want to allow non-local connections, you need to add more
  # "host" records.  In that case you will also need to make PostgreSQL
  # listen on a non-local interface via the listen_addresses
  # configuration parameter, or via the -i or -h command line switches.
  host    redmine         redmine         127.0.0.1/32            md5
  host    redmine         redmine         ::1/128                 md5
  ```

  設定完了  

* **PostgreSQLの起動および自動起動の設定**  

  ```
  $ service postgresql start
  Redirecting to /bin/systemctl start  postgresql.service
  $ systemctl enable postgresql
  Created symlink from /etc/systemd/system/multi-user.target.wants/postgresql.service to /usr/lib/systemd/system/postgresql.service.
  ```

* **PostgreSQLのディレクトリに移動**  
rootユーザーで`cd /var/lib/pgsql`  

* **Redmineユーザーの作成**  

  ```
  $ sudo -u postgres createuser -p redmine
  新しいロールのためのパスワード：
  もう一度入力してください：
  ```

  ※PostgreSQLのユーザー　redmine　には任意のパスワードを設定する。このパスワードは後述のdatabase.ymlの設定でも使用する。  

* **Redmine用データベースの作成**  

  `$ sudo -u postgres createdb -E UTF-8 -l ja_JP.UTF-8 -O redmine -T template0 redmine`  

* **PostgreSQLのディレクトリから元のディレクトリに戻る**  

  `$ cd -`

## 6. Redmineのインストール
* **Redmineのダウンロード**  

  gitコマンドを使って /var/lib/redmine にソースコード一式をダウンロードする。  

  `$ git clone https://github.com/redmine/redmine.git /var/lib/redmine`  

* **データベースへの接続設定**  

  Redmineからデータベースへ接続するための設定ファイルを作成する。  

  /var/lib/redmine/config にdatabase.yml作成する。  

  `$ touch database.yml`  

  作成したdatabase.ymlに下記の内容を記述  

  ```
  production:
   adapter: postgresql
   database: redmine
   host: localhost
   username: redmine
   password: "********"
   encoding: utf8
  ```

  ※rootユーザーでないとファイルに書き込めない可能性がある。  
  ※password: は、PostgreSQL上に作成したredmine用ユーザーのパスワード  
  ※database.yml.exampleに設定例が記載されているので参考にしてみる。  

* **redmineインストールディレクトリへ移動**  

  `$ cd /var/lib/redmine`  

* **gemパッケージのインストール**  

  Rubyのパッケージ管理ツール「bundler」を使用してRedmineが依存するgemパッケージをインストールする。  

  `$ bundle install --without development test --path vendor/bundle`  

## 7. Redmineの初期設定と初期データ登録  
Redmine動作に関する初期設定と初期データの登録を行う。  
rootユーザーで下記コマンドを /var/lib/redmine ディレクトリで実行する。  

* **セッション改ざん防止用秘密鍵の作成**  

  下記コマンド実行  

  `$ bundle exec rake generate_secret_token`  

* **データベースのテーブル作成**  

  database.yml で指定したデータベースにテーブルを作成する。  

  `$ RAILS_ENV=production bundle exec rake db:migrate`

* **デフォルトデータの登録**  

  作成したテーブルにデフォルトデータのロードを行う。  
  この操作によりトラッカー、優先度、ステータス、ロール、ワークフローなどの初期値が登録される。  

  `$ RAILS_ENV=production REDMINE_LANG=ja bundle exec rake redmine:load_default_data`

## 8. Passengerのインストール
Phusion Passenger をインストールする前に`rbenv rehash`コマンドを実行しておく。  

Apache上でRedmineなどのRailsアプリケーションを実行するために使われる、  
Phusion Passenger をインストールする。  
```
$ gem install passenger --no-rdoc --no-ri
Fetching: rack-2.0.3.gem (100%)
/usr/local/src/rbenv/rbenv.d/exec/gem-rehash/rubygems_plugin.rb:6: warning: Insecure world writable dir /usr/local/src/rbenv in PATH, mode 040777
Successfully installed rack-2.0.3
Fetching: passenger-5.1.11.gem (100%)
Building native extensions.  This could take a while...
Successfully installed passenger-5.1.11
2 gems installed
```

* **PassengerのApache用モジュールのインストール**  

  下記コマンドを実行してApache用のモジュールのビルドとインストールを行う。  

  `$ passenger-install-apache2-module --auto --languages ruby`  

  しばらく待つ。  

* **Apache用設定内容の確認**  

  下記コマンドを実行するとApacheに追加すべき設定が表示される。この情報はこの後Apacheの設定を行う時に使用する。  

  `$ passenger-install-apache2-module --snippet`   

## 9. Apacheの設定
rootユーザーで/etc/httpd/conf.d/redmine.confを作成して下記の内容を記述。  

`touch /etc/httpd/conf.d/redmine.conf`コマンドで作成可能  

```
# Redmineの画像ファイル・CSSファイル等へのアクセスを許可する設定。
# Apache 2.4のデフォルトではサーバ上の全ファイルへのアクセスが禁止されている。
<Directory "/var/lib/redmine/public">
  Require all granted
</Directory>

# Passengerの基本設定。
# passenger-install-apache2-module --snippet で表示された設定を記述。
# 環境によって設定値が異なるため以下の3行はそのまま転記せず、必ず
# passenger-install-apache2-module --snippet で表示されたものを使用すること。
#
LoadModule passenger_module /usr/local/src/rbenv/versions/2.4.2/lib/ruby/gems/2.4.0/gems/passenger-5.1.11/buildout/apache2/mod_passenger.so
<IfModule mod_passenger.c>
  PassengerRoot /usr/local/src/rbenv/versions/2.4.2/lib/ruby/gems/2.4.0/gems/passenger-5.1.11
  PassengerDefaultRuby /usr/local/src/rbenv/shims/ruby
</IfModule>
```
**※CentOSではApache起動時に /etc/httpd/conf.d/*.confが自動的に読み込まれます。**  

* **Apacheの起動および自動起動の設定**  

  ```
    $ service httpd start
    Redirecting to /bin/systemctl start  httpd.service
    Job for httpd.service failed because the control process exited with error code. See "systemctl status httpd.service" and "journalctl -xe" for details.

    $ systemctl enable httpd
    Created symlink from /etc/systemd/system/multi-user.target.wants/httpd.service to /usr/lib/systemd/system/httpd.service.
  ```

## 10. Apache上のPassengerでRedmineを実行するための設定
Redmineを配置したディレクトリ以下のファイルを、Apacheを実行するユーザー・グループ(CentOSの場合はいずれも"apache")で読み書きできるよう、下記コマンドでオーナーを変更します。  
rootユーザーで下記コマンドを実行  

`chown -R apache:apache /var/lib/redmine`  

* **サブディレクトリでRedmineを実行させる**  

  URLのサブディレクトリ名でURLにアクセスできるように設定する。  
  例：http://サーバーIPアドレス又はホスト名/サブディレクトリ名  

  * **シンボリックリンクの作成**  

    Apacheの DoucumentRoot に指定されているディレクトリ(**デフォルトは/var/www/html**)にRedmineのpublicディレクトリ(**/var/lib/redmine/public**)のシンボリックリンクを作成する。シンボリックリンクの名前はURLのサブディレクトリ名部分で使用したい名前にする。  
    下記コマンドでシンボリックリンクを /var/www/html/ に作成する。  

    `ln -s /var/lib/redmine/public /var/www/html/使用したい名前(例：redmine)`  

  * **Apacheへの設定追加**  

    前述の手順で作成したRedmine関係のApacheの設定ファイル  
    /etc/httpd/conf.d/redmine.conf に以下の設定を追加する。  

    ```
    Alias /redmine /var/lib/redmine/public
    <Location /redmine>
     PassengerBaseURI /redmine
     PassengerAppRoot /var/lib/redmine
    </Location>
    ```

    設定後、下記コマンドを実行してApacheを再起動してください。  

    ```
    $ service httpd configtest

    $ service httpd restart
    ```

以上でRedmineインストール作業完了です。
