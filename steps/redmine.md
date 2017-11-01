# CentOS7にRedmineをインストールする

<<<<<<< HEAD
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

  `$ yum -y install curl-devel libyaml-devel libffi-devel`  

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
  $ service start postgresql.service
  Redirecting to /bin/systemctl start  postgresql.service

  $ systemctl enable postgresql.service
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
=======
## Redmineとは
「Redmine」とは、プロジェクト管理ができるオープンソースソフトウェアです。  
例えば、誰が何をする、などどいったことは「チケット」を用いてタスクを管理することができます。

Redmineを利用するには以下をインストールする必要があります。  
　　・Redmine  
　　・データベース  
　　・Webサーバ  

今回は、データベースに、**PostgreSQL**、webサーバに**apache**を適用します。  
**※今回のコマンドはすべてrootユーザで行うものとします。**

## 事前準備
  1. SELinuxを無効化します。  
  SELinuxは、仮想マシンをサーバとして利用する際に、多くの制約がかかるので、無効化しておきます。　  
  下記コマンドを実行しSELinuxの状態を確認、無効化します。

```
  $ getenforce
    Enforcing  #有効化されている

  $ vi /etc/selinux/config
    # This file controls the state of SELinux on the system.  
    # SELINUX= can take one of these three values:  
    #     enforcing - SELinux security policy is enforced.  
    #     permissive - SELinux prints warnings instead of enforcing.  
    #     disabled - No SELinux policy is loaded.  
    #SELINUX=enforcing    ←コメント化  
    SELINUX=disabled      ←追記  
    # SELINUXTYPE= can take one of three two values:  
    #     targeted - Targeted processes are protected,  
    #     minimum - Modification of targeted policy. Only selected processes are protected.  
    #     mls - Multi Level Security protection.  
    SELINUXTYPE=targeted  

  ファイル保存後、再起動。
  $ reboot

  再起動後SELinuxが無効化されているか確認。
  $ getenforce
    Disabled  #無効化されている

```

  2. firewalldでHTTP接続を許可させます  
  初期状態ではFirewalld（ファイアーウォール）が有効になっており外部からHTTP接続ができません。  
  WebブラウザからアクセスできるようにFirewalldの設定を変更します。  
  以下コマンドを実行してください。

```

  $ firewall-cmd --zone=public --add-service=http --permanent
    success  #変更に成功

  変更内容を反映させる
  $ firewall-cmd --reload
    success  #反映に成功

  反映の確認
  $ firewall-cmd --zone=public --list-services
    dhcpv6-client ssh http  #外部からの通信を許可されている一覧（httpが追加されていればOK）

```

  3. 続いて必要なパッケージをインストールします。  
  Redmineを使用するには、  
　　　・開発ツール  
　　　・Passengerのビルドに必要なファイル  
　　　・PostgreSQL  
　　　・Apache  
　　　・ImageMagick(画像を操作したり表示したりするためのソフトウェア)と日本語フォント  
    のインストールが必要です。  
  ※Passengerとは、Railsをwebサーバ上で動かすために必要なモジュールです。

  まずは以下コマンドにて、開発ツールのインストールを行います。

```

  $ yum -y groupinstall "Development Tools"

```

  次にPassengerのビルドに必要なファイルをインストールします。

```

  $ yum -y install curl-devel libyaml-devel libffi-devel

```

  つづいてPostgreSQLのインストールをします。

```

  $ yum -y install postgresql-server postgresql-devel

```

  Apacheのインストールをします。

```
  $ yum -y install httpd httpd-devel

```

  最後にImageMagickと日本語フォントのインストールをします。

```

  yum -y install ImageMagick ImageMagick-devel ipa-pgothic-fonts

```

以上でredmineを動作させる為のツールのインストールは終了しました。

## インストールしたツールの設定
  先ほどインストールしたツールを環境に合わせるため設定していきます。  

  事前にbundlerのインストールをするため下記コマンドを実行します。  
  bundlerはgemパッケージをインストールするのに利用されます。

```

  $ gem install bundler --no-rdoc --no-ri

```
　　※`--no-rdoc -no-ri`を付けるとドキュメントのインストールを省略し、インストール時間が早く済みます。

### PostgreSQLの設定

  1. データベースの格納領域（データベースクラスタ）を初期化するため、下記コマンドを実行します。

```

  $ postgresql-setup initdb
    Initializing database ... OK  #初期化の成功

```

  2. 続いてRedmineからPostgreSQlに接続するための設定を追加します。  
    /var/lib/pgsql/data/pg_hba.confを開き下記内容を追加してください。

```

  $ vi /var/lib/pgsql/data/pg_hba.conf
    # PostgreSQL Client Authentication Configuration File
    # ===================================================
    #
    # Refer to the "Client Authentication" section in the PostgreSQL
    # documentation for a complete description of this file.  A short
    # synopsis follows.
                            ・
                            ・
                            ・
    # Put your actual configuration here       ←を探す（67行目くらい）
    # ----------------------------------
    #
    # If you want to allow non-local connections, you need to add more
    # "host" records.  In that case you will also need to make PostgreSQL
    # listen on a non-local interface via the listen_addresses
    # configuration parameter, or via the -i or -h command line switches.
    host    redmine        redmine        127.0.0.1/32        md5         #追記
    host    redmine        redmine        ::1/128             md5         #追記

```

  3. PostgreSQLの起動と、自動起動をするように設定します。

```

  $ systemctl start postgresql.service    #PostgreSQLの起動

  $ systemctl enable postgresql.service    #PostgreSQLの自動起動設定

```

  4. PstgreSQLのディレクトリに移動し、Redmine用のユーザを作成します。

```

  $ cd /var/lib/pgsql

  $ sudo -u postgres createuser -P redmine
    新しいロールのためのパスワード:[任意のパスワードを入力]
    もう一度入力してください:[パスワード確認]

```

  パスワードはPostgreSQLの設定ファイルに記述するので覚えておきましょう。

  5. 最後にRedmine用のデータベースを作成します。

```

  $ sudo -u postgres createdb -E UTF-8 -l ja_JP.UTF-8 -O redmine -T template0 redmine

  $ cd -    #PostgreSQLのディレクトリから元のディレクトリに戻っておく

```

### Redmineのインストール

  Redmineはsubversionにあるリポジトリをチェックアウト(ソースコードのダウンロード)することによって、  
  仮想環境上で動作させることができるようになります。  
  また、svnコマンドを利用することでsubversionのリポジトリをチェックアウトしたり、コミット、更新ができます。

  1. Redmineのリポジトリをチェックアウトするため、下記コマンドを実行してください。  
  今回は`/var/lib/redmine`にチェックアウトしますが、他のディレクトリにチェックアウトしたい場合は、適宜読み替えてください。

```

  $ svn checkout https://svn.redmine.org/redmine/branches/3.4-stable/ /var/lib/redmine

```

`checkout`は`co`でも`checkout`として扱われます。

  2. Redmineからデータベースへ接続するための設定を行います。
    先ほどチェックアウトしたディレクトリに移動し、config配下に設定ファイル（database.yml）を作成します。
    作成した「database.yml」に以下内容を追記してください。

```

  $ cd /var/lib/redmine

  $ vi config/database.yml
    ## 下記内容を追記
    production:
      adapter: postgresql   #先頭空白2
      database: redmine     #先頭空白2
      host: localhost       #先頭空白2
      username: redmine     #先頭空白2
      password: "********"  #PosgreSQL上に作成したredmine用ユーザのパスワード(先頭空白2)
      encoding: utf8        #先頭空白2
    ## ここまで

```

  また、メールサーバへの接続設定、日本語フォントファイルへのパスを記述したファイルも追加しておきます。

```

  $ vi config/configuration.yml
    ##以下内容を追記
    production:
    email_delivery:
      delivery_method: :smtp   #先頭空白2
      smtp_settings:           #先頭空白2
        address: "localhost"   #先頭空白4
        port: 25               #先頭空白4
        domain: "localhost"    #Redmineを実行するサーバのFQDN(先頭空白4)

      rmagick_font_path: /usr/share/fonts/ipa-pgothic/ipagp.ttf(先頭空白2)
    ## ここまで
```

**.ymlファイルはインデントがずれているとエラーが出るので、先頭の空白や半角/全角などに注意して記述しましょう。**

  3. 最後にgemパッケージのインストールを行います。  
  gemとはRubyのライブラリを利用するために必要なソフトウェアです。gemにRails開発が楽にできるようになります。  
  インストールするgemパッケージは`Gemfile`に書かれています。また、新たにインストールするときも`Gemfile`に記述します。

　　ではgemパッケージをインストールします。
```

  $ bundle install --without development test --path vendor/bundle
    # development,testに書かれているgemパッケージのインストールは省略します。
    # --path vendor/bundleでインストール先のパスを指定します。

```

### Redmineの初期設定・初期データの登録

  1. セッションを改ざんされ、個人情報などを不正に取得・利用されることを防ぐため、防止用の秘密鍵を作成します。
```

  $ bundle exec rake generate_secret_token

```

  2. 続いてデータベースのテーブルを作成します
```

  $ RAILS_ENV=production bundle exec rake db:migrate

```

  3. 次に作成したテーブルに初期データを投入します。  
     初期データにはチケットのトラッカー、ステータスなどが登録されています。

```

  $ RAILS_ENV=production REDMINE_LANG=ja bundle exec rake redmine:load_default_data
    Default configuration data loaded.    #投入の成功

```

### Passengerのインストール

  前述にもあった通りPassengerとは、RailsアプリケーションをWebサーバ上で実行するために使われるモジュールです。

  1. まずはpassengerをインストールします。
```

  $ gem install passenger --no-rdoc --no-ri

```

  2. 次にPassengerのApache用モジュールをインストールします。
```

  $ passenger-install-apache2-module --auto --languages ruby

```

  3. Apache用設定内容の確認をします。
```

  $ passenger-install-apache2-module --snippet
    #ここで表示される内容はApacheの追加するべき設定です

```

### Apacheの設定

  1. `/etc/httpd/conf.d/redmine.conf`を作成し、以下の内容を追記します。

```

  $ vi /etc/httpd/conf.d/redmine.conf
    ## 画像ファイルなどへのアクセスを許可する設定
    # ここから追記
    <Directory "/var/lib/redmine/public">
      Require all granted
    </Directory>

    ## Passengerの基本設定
    # この部分はpassenger-install-apache2-module --snippetで表示された部分を追記するので転記はしないこと。
    LoadModule passenger_module /usr/local/src/rbenv/versions/2.4.2/lib/ruby/gems/2.4.0/gems/passenger-5.1.11/buildout/apache2/mod_passenger.so
    <IfModule mod_passenger.c>
      PassengerRoot /usr/local/src/rbenv/versions/2.4.2/lib/ruby/gems/2.4.0/gems/passenger-5.1.11
      PassengerDefaultRuby /usr/local/src/rbenv/versions/2.4.2/bin/ruby
    </IfModule>

```

  2. Apacheの起動と、自動起動の設定を行います。

```

  $ systemctl start httpd.service     #Apacheの起動

  $ systemctl enable httpd.service    #Apacheの自動起動設定

```

### Redmineを実行するための設定

  1. Redmineを配置したディレクトリ以下のファイルをapacheで実行できるように、オーナーを変更します。

```

  $ chown -R apache:apache /var/lib/redmine

```

  2. 次にWebブラウザからRedmineにアクセスできるように設定をします。  
  方法はいくつかありますが、今回はURLのサブディレクトリでアクセスできるように（http://サーバIPアドレス/redmine/）  
  設定をします。  
  この方法は、いくつかのシステムを同じ仮想環境で実行するときに便利な設定です。

  前の手順で作成した、RedmineでのApacheの設定ファイル`/etc/httpd/conf.d/redmine.conf`に下記内容を追記します。

```

  $ vi /etc/httpd/conf.d/redmine.conf
    <Directory "/var/lib/redmine/public">
      Require all granted
    </Directory>
                  ・
                  ・
                  ・
    # ここから追記
    Alias /redmine /var/lib/redmine/public
    <Location /redmine>
      PassengerBaseURI /redmine
      PassengerAppRoot /var/lib/redmine
    </Location>
    # ここまで

```

  3. 2のファイルを保存後、Apacheを再起動します。

```

  $ systemctl restart httpd.service

```

  これで、`http://サーバのIPアドレス/redmine`でRedmineへアクセスできるようになります。

## Redmineアクセス後

  Redmineにアクセスできたらホームが表示されます。  
  ここからログインをしてみます。  

  1. ログインは右上の「ログイン」からできます。
<div align=center>
  <img src=image/redmine_home.JPG>
</div>  

  現在初期データとして、管理者ユーザ（admin）が登録されています。  
　　　　ログインID:admin  
　　　　パスワード:admin  
  でログインができます。

  2. ログインをするとパスワードを変更する画面が表示されるので、任意のパスワードに必ず変更してください。

  3. パスワードを変更すると、管理者ユーザ（admin）の詳細ページが表示されます。  
     これ以降、プロジェクトの開始や、チケットの登録などができるようになります。  
     詳しい使い方などはRedmine公式サイトを参照(http://redmine.jp/tech_note/first-step/user/)
>>>>>>> upstream/master
