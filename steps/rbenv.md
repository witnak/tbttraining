# CentOS7にrbenvをインストールする
## 前提
  git cloneを使用するので、gitがインストールされていない場合は`sudo yum install -y git`でイン   ストールする。
  <br>
  <br>
  基本はローカルユーザーで操作する。
  <br>
  <br>
  下記のパッケージをインストールする。<br>
  `sudo yum install -y openssl-devel readline-devel zlib-devel gcc`<br>
  ※インストールしておかないとRubyインストール時にエラーを吐かれる。
  <br>
  <br>

## 1. rbenvのインストール
  下記コマンドは1番上の階層のディレクトリで実行する。<br>
  ```
  $ cd / #一番上の階層に移動
  $ git clone https://github.com/rbenv/rbenv.git usr/local/src/rbenv
   #rbenvをインストールするための記述
  $ git clone https://github.com/rbenv/ruby-build.git usr/local/src/rbenv/plugins/ruby-build
   #ruby-buildをインストールするための記述
  ```

  rbenv・・・Rubyのバージョンを切り替えて使うための環境を提供してくれるツール<br>
  ruby-build・・・rbenvのプラグインで、Rubyをインストールするために使われる。<br><br>
  ※バージョンが古い可能性があるので、rbenv、ruby-buildのディレクトリで下記コマンドを実行して更新<br>
  `git pull origin master`<br>

## 2. rbenvを使用できるように環境変数の登録を行う
  etc/profile.d/の直下にrbenv.shというshellスクリプトファイルを作成する。<br>
  下記コマンドで作成も同時に行ってくれる。
  ```
  $ echo 'export RBENV_ROOT="/usr/local/src/rbenv/"' >> /etc/profile.d/rbenv.sh　
  $ echo 'export PATH="${RBENV_ROOT}/bin:${PATH}"' >> /etc/profile.d/rbenv.sh
  $ echo 'eval "$(rbenv init -)"' >> /etc/profile.d/rbenv.sh
  $ cat /etc/profile.d/rbenv.sh　　#上記の内容が表示されればOK
  $ source /etc/profile.d/rbenv.sh　　#問題なければ再読み込み
  $ rbenv --version　　#バージョン確認ができればOK
  $ su           　　#一旦rootユーザーに切り替えます
  $ source /etc/profile.d/rbenv.sh　　#rootユーザーで使えるように反映させます
  $ rbenv --version　　#バージョンが確認ができればOK
  $ exit             #ローカルユーザーに戻る
  ```

## 3. Rubyのインストール
  **Rubyのインストール**
  * `rbenv install -l`<br>
  インストールできるRubyのバージョン一覧を表示
  * `rbenv install バージョン`<br>
  指定したバージョンのRubyがインストールされる。
  * `ruby -v`<br>
  ※コマンドがないと言われるがバージョンが表示されれば問題ない。

## 4. 使用するRubyのバージョンを決める
1. **システム全体で使う場合**<br>
  `rbenv global バージョン`<br>
  インストール済みのRubyからバージョンを指定して変更可能
  <br>
  <br>

2. **プロジェクト単体でRubyのバージョンを変える場合**<br>
  `rbenv local バージョン`<br>
  上記のコマンドを実行する際は対象のプロジェクトのディレクトで行う。
  <br>
  <br>

#### ※Rubyのバージョンが確認できない場合
  バージョン確認ができない場合は /usr/local/src/ まで移動して下記コマンドを実行してパーミッション変更<br>
  `sudo chmod 777 rbenv`<br

#### ※ローカルユーザーでRubyのバージョン変更したい場合
  /usr/local/src/rbnev/　のversionファイルのパーミッションを下記コマンドで変更<br>
  `sudo chmod 777 version`<br>
  これでバージョン変更が可能になる。<br><br>
