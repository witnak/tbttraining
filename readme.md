# 環境構築手順

各種環境を構築する手順を記述する。

## 事前準備

* [Githubアカウント取得](github/create_account.md)
* [Gitのインストール](github/localgit.md)
* [forkの手順](github/fork.md)
* [ローカルへクローン](github/cloneintolocal.md)
* [fork元の更新に追いつく](github/setupstream.md)

## VirtualBoxでの開発環境構築

* [VirtualBoxをインストールする](steps/virtualbox_install.md)
* CentOS7を仮想環境に構築する
  * [ホストPCとブリッジで接続する](steps/centos7_b.md)
  * [ホストPCとポートフォワードで接続する](steps/centos7_p.md)
* [CentOS7にrbenvをインストールする](steps/rbenv.md)
* [CentOS7にRedmineをインストールする](steps/redmine.md)
* [CentOS7にMariaDBをインストールする](steps/mariadb_install.md)
* RedmineのデータベースをPostgresqlからMariaDBへ切り替える
* CentOS7にnginxをインストールしてapacheと切り替える
* CentOS7にunicornをインストールしてRedmineと連携させる
* CentOS7にpumaをインストールしてRedmineと連携させる

## VirtualBox Tips

* 別のマシンからVirtualBoxで走っている仮想マシンにアクセスできるようにする
* VirtualBox内に二つの仮想マシンを作り、それぞれDBサーバとアプリケーションサーバとする

## Dockerでの開発環境構築

* [Windows10 Pro 以上でのDockerインストール](docker/docker_for_windows.md)
* [Windows10 home または Windows8 以下でのDockerインストール](docker/docker_toolbox_for_win.md)
* MacでのDockerインストール

* [DockerによるRails動作環境を作る](docker/rails_on_docker.md)
