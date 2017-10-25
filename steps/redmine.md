# CentOS7にRedmineをインストールする

## 1. SELinuxを無効にする
SELinuxのアクセス制限によってインストール作業や運用でつまづく可能性があるので無効にする。<br><br>
rootユーザーで /etc/sysconfig/selinux　を開き、SELINUX=　の値をdisabledに変更する。<br>
<img src=image/SELinux.JPG><br>
変更後はCentOSを再起動<br>
再起動後、`getenforce`コマンドを実行してSELinuxが無効になったことを確認する。<br>
Disabledと表示されればSELinuxは無効になっている。

## 2. firewalldでHTTPを許可
CentOS7の初期状態ではFirewalld（ファイアウォール）が有効になっており、外部からサーバー上のHTTPにに接続することができない。クライアントのwebブラウザからアクセスできるようにFirewalldの設定を変更する<br>
<br>
* デフォルトzoneにhttpを追加する<br>
```
$firewall-cmd --zone=public --add-service=http --permanent
success
```
