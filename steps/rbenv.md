# CentOS7にrbenvをインストールする

## rbenvとは
  「rbenv」とは異なるrubyのバージョンを管理するツールです。  
  「rbenv」を利用することで、プロジェクトごとにrubyのバージョンを変更して、開発を行えるようになります。  

**ではrbenvのインストールを行います**

## rbenvのインストール

  ひとまずはrootユーザでのみ利用できるようにインストールを行います。

  1. 事前準備としてyumのアップデートを行います。  
  まずrootユーザになるターミナルで、  
```
  $ su -
```
  を実行してください。  
  （yumとは、様々なパッケージをインストール・アップデートなどが行えるパッケージ管理システムです。）

  2. その後、実際にアップデートと、rbenvを利用するのに必要なパッケージをインストールするため、  
```
  $ yum -y update

  $ yum -y install git gcc gcc-c++ zlib-devel openssl-devel readline-devel  

```

  を実行します。

  3. 続いてrbenvのインストールを行います。githubのリポジトリをホームディレクトリにクローンします。  
```

  $ git clone https://github.com/sstephenson/rbenv.git /usr/local/src/rbenv

```
  を実行します。

  4. 次に、ユーザの環境変数を設定するファイル（rbenv.sh)を作成し、パスを追加します。  
  以下のコマンドをひとつずつ実行してください。
```
  $ echo 'export RBENV_ROOT="/usr/local/src/rbenv/"' >> /etc/profile.d/rbenv.sh

  $ echo 'export PATH="${RBENV_ROOT}/bin:${PATH}"' >> /etc/profile.d/rbenv.sh

  $ echo 'eval "$(rbenv init -)"' >> /etc/profile.d/rbenv.sh

  $ source /etc/profile.d/rbenv.sh
```
  5. 正常にインストールできたか確認するためrbenvのバージョンを確認します
```
  $ rbenv --version
  rbenv 1.1.1-6-g2d7cefe
```

## rubyのインストール

  ここからは実際にrubyのインストールを行います。

  1. rbenvコマンドを使ってrubyをインストールできるように「ruby-build」を追加します。  
  rbenvと同じくgithubのリポジトリをクローンします。
```
  $ git clone https://github.com/sstephenson/ruby-build.git /usr/local/src/rbenv/plugins/ruby-build
```
  を実行します。

  2. インストールできるrubyのバージョンを確認します。
```
  $ rbenv install --list
    1.8.5-p52
    1.8.5-p113
    1.8.5-p114
        ・
        ・
        ・
    2.4.0
    2.4.1
    2.4.2           ←今現在の最新版
    2.5.0-dev
    2.5.0-preview1
        ・
        ・
        ・
```

  3. 先ほど調べた、最新版（安定版）のruby(2.4.2)をインストールします。
```
  $ rbenv install -v 2.4.2

```

  4. ruby(2.4.2)がインストールされたかを確認します。
```
  $ rbenv versions
    2.4.2
```

  5. 続いて旧バージョンのruby(2.3.3)を先ほど同様インストールします。
```
  $ rbenv install -v 2.3.3

```

  6. ruby(2.3.3)がインストールされたか確認をします。
```
  $ rbenv versions
    2.3.3
    * 2.4.2 (set by /usr/local/src/rbenv/version)
```
  `*`が現在のバージョンを示します。

  7. ruby(2.4.2)からruby(2.3.3)へ切り替えを行います。
```
  $ ruby -v
    ruby 2.4.2p198 (2017-09-14 revision 59899) [x86_64-linux]

  $ rbenv global 2.3.3

  $ ruby -v
    ruby 2.3.3p222 (2016-11-21 revision 56859) [x86_64-linux] ←ruby(2.3.3)へ切り替わっている。
```
  `ruby -v`の結果が`ruby 2.3.3 ...`になっていればrbenvによるrubyのバージョンの切り替えができています。

## 全ユーザ共通でrbenvを利用する
  現在の設定では`su -`でrootユーザになってからでないとrbenvを利用できません。  
  ローカルユーザでも利用できるよう設定を行います。

  1. `su -`でrootユーザになっている場合`exit`で抜けます。

  2. 以下コマンドを実行し、環境変数の設定を適用します。
```

  $ source /etc/profile.d/rbenv.sh

```

  3. rbenvが利用できるかどうか確認するため、以下コマンドを実行します。
```

  $ rbenv --version
    rbenv 1.1.1-6-g2d7cefe

```

  4. rootユーザでインストールしたrubyが適用されているか確認をします。
```

  $ rbenv versions
    * 2.3.3 (set by /usr/local/src/rbenv/version)
    2.4.2

  $ ruby -v
    ruby 2.3.3p222 (2016-11-21 revision 56859) [x86_64-linux]

```

  5. rubyをローカルユーザでインストールする場合はsudoでroot権限を持ってからインストールをします。
```

  $ sudo env "PATH=$PATH" rbenv install -v [バージョン]
    ↑環境変数を維持するため「env "PATH=$PATH"」を追記します。

```
  ローカルユーザでインストールしたrubyはrootユーザでも、  
  rootユーザでインストールしたrubyはローカルユーザでも利用できるようになっています。
