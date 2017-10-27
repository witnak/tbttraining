# CentOS7にMariaDBをインストールする

## MariaDBとは
  MariaDBとは、MySQLから派生したオープンソースのリレーショナルデータベースです。  
  MySQLとの互換性を保ちつつ、性能などを高めるため、独自の機能を備えています。  
  MySQLからMariaDBへの移行が容易であることから、MariaDBユーザは増えています。

## MariaDBをインストールする
  **今回のコマンドはrootユーザで行うものとします。**  
  1. まずはMariaDBをインストールするので以下のコマンドを実行してください。

```
  $ yum -y install mariadb mariadb-server

```

  2. インストールできたか確認するため、以下のコマンドを実行します。

```

  $ rpm -qa | grep -i "MariaDB"
    mariadb-5.5.56-2.el7.x86_64
    mariadb-libs-5.5.56-2.el7.x86_64
    mariadb-server-5.5.56-2.el7.x86_64

```

  上記3件が表示されればOKです。

  3. インストールが完了したら、MariaDBの起動と自動起動の設定を行います。

```

  $ systemctl start mariadb     #MariaDBの起動

  $ systemctl enable mariadb    #MariaDBの自動起動設定

```

## MariaDBの初期設定

  1. 下記コマンドを実行し、初期設定を行います。

```

  $ mysql_secure_installation
    In order to log into MariaDB to secure it, we'll need the current
    password for the root user.  If you've just installed MariaDB, and
    you haven't set the root password yet, the password will be blank,
    so you should just press enter here.

    Enter current password for root (enter for none): [Enter]
    OK, successfully used password, moving on...

    Setting the root password ensures that nobody can log into the MariaDB
    root user without the proper authorisation.

    Set root password? [Y/n] y[Enter]
    New password: [任意のパスワード]
    Re-enter new password: [パスワード再入力]
    Password updated successfully!
                          ・
                          ・                 これ以降はy[Enter]を繰り返します。
                          ・

    All done!  If you've completed all of the above steps, your MariaDB
    installation should now be secure.

    Thanks for using MariaDB!

```

  2. 続いて文字化けを防止するため、設定ファイルを編集します。

```

  $ cd /etc/my.cnf.d/

  $ vi server.cnf
    #
    # These groups are read by MariaDB server.
    # Use it for options that only the server (but not clients) should see
                      ・
                      ・
                      ・
    # this is only for the mysqld standalone daemon
    [mysqld]
    # ここから追記
    character-set-server = utf8
    # ここまで

```

  3. 設定を反映するためMariaDBを再起動します。

```

  $ systemctl restart mariadb

```

以上で初期設定は終了です。

## MariaDBにログイン

 1. MariaDBにログインするため以下のコマンドを実行します

```

  $ mysql -u root -p
  Enter password:[パスワード入力][Enter]
  Welcome to the MariaDB monitor.  Commands end with ; or \g.
  Your MariaDB connection id is 2
  Server version: 5.5.56-MariaDB MariaDB Server

  Copyright (c) 2000, 2017, Oracle, MariaDB Corporation Ab and others.

  Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

  MariaDB [(none)]>

```
  これでログインに成功しました。

  2. MariaDBが利用できるか試すため、新たなデータベース(training)を作成、確認をします。  
     ※ MariaDBのコマンドは、基本的に末尾に「；」を付けます。

```

  $ MariaDB [(none)]> create database training;
    Query OK, 1 row affected (0.00 sec)

  $ MariaDB [(none)]> show databases;
    
    +--------------------+
    | Database           |
    +--------------------+
    | information_schema |
    | mysql              |
    | performance_schema |
    | training           |    #trainingが追加されている
    +--------------------+
    4 rows in set (0.03 sec)

  # MariaDBからログアウトするときは、「exit」を入力します。
  # ここでは「;」はつけません。
  $ MariaDB [(none)]> exit
    Bye

```

  ここまででMariaDBを利用できることがわかりました。  
  MariaDBのコマンドは基本的にMySQLのSQL文を利用することができます。  
  たとえば、データを挿入するときは「INSERT INTO」、ある条件をもとにデータを取ってくるときは、「SELECT ~ FROM ~ WHERE 」  
  といったようにMySQLのSQL文をMaraiaDBで利用します。