# CentOS7にRedmineをインストールする

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
  下記コマンドを実行しSELinuxの状態を確認します。

```SELinuxの無効化設定
  $ getenforce
    Enforcing ←有効化されている

  $ vi /etc/selinux/config
    # This file controls the state of SELinux on the system.  
    # SELINUX= can take one of these three values:  
    #     enforcing - SELinux security policy is enforced.  
    #     permissive - SELinux prints warnings instead of enforcing.  
    #     disabled - No SELinux policy is loaded.  
    #SELINUX=enforcing    ←コメント化  
    SELINUX=disabled      ←追記  
    ### Disalbe SELINUX end  
    # SELINUXTYPE= can take one of three two values:  
    #     targeted - Targeted processes are protected,  
    #     minimum - Modification of targeted policy. Only selected processes are protected.  
    #     mls - Multi Level Security protection.  
    SELINUXTYPE=targeted  

  ファイル保存後、再起動。
  $ reboot

  再起動後SELinuxが無効化されているか確認。
  $ getenforce
    Disabled ←無効化されている

```
  2. firewalldでHTTPを許可させます  
  初期状態ではFirewalld（ファイアーウォール）が有効になっており外部からHTTP接続ができません。  
  WebブラウザからアクセスできるようにFirewalldの設定を変更します。  
  以下コマンドを実行してください。
```

  $ firewall-cmd --zone=public --add-service=http --permanent
    success ←変更に成功

  変更内容を反映させる
  $ firewall-cmd --reload
    success ←反映に成功

  反映の確認
  $ firewall-cmd --zone=public --list-services
    dhcpv6-client ssh http ←外部からの通信を許可されている一覧（httpが追加されていればOK）

```

