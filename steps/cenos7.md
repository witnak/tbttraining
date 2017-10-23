# CentOS7を仮想環境に構築する

前提として、VirtualBoxのインストールが完了していること。

## ISOイメージファイルをダウンロードする
1. CentOS７環境構築に必要となる、ISOイメージファイルのダウンロードを行うため、下記サイトにアクセスします。
  https://www.centos.org/download/  
※ISOイメージファイルとはCDやDVDなどの中身を1つにまとめたファイルのことで、  
  CentOSのインストールに必要なファイルが入っている。

2. 「DVD」をクリックしイメージファイル選択画面へ
<div align=center>
  <img src=image/CentOS7_ISO.jpg>
</div>

3. Actual Country の一番上を選択するとダウンロードが始まります。
<div align=center>
  <img src=image/CentOS7_ISO_download.jpg>
</div>

#### これでISOイメージファイルのダウンロードは終了です。  

## 仮想マシンの新規作成
1. VirtulBox左上の「新規」を選択します。  
<div align=center>
  <img src=image/CentOS7_create.jpg>
</div>

2. 仮想マシンの名前とOSの選択を行います。  
<div align=center>
  <img src=image/CentOS7_name.jpg>
</div>  
今回はLinuxのインストールになるので、タイプはLinux、バージョンはRed Hat(64-bit)を選択し、「次へ」を選択します。  

3. 仮想メモリのサイズを設定します。  
今回は1024MBのまま、「次へ」を選択します。  
<div align=center>
  <img src=image/CentOS7_size.jpg>
</div>  


4. ハードディスクの作成方法を選択します。  
新規で作成する場合は、仮想ハードディスクを作成する（C）にチェックを入れ、「作成」を選択します。  
<div align=center>
  <img src=image/CentOS7_harddisc.jpg>
</div>  
 

5. ハードディスクのファイルタイプを選択します。  
今回はVMDKにチェックを入れ「作成」を選択します。  
<div align=center>
  <img src=image/CentOS7_type.jpg>
</div>  


6. ハードディスクにあるストレージの設定を行います。  
初期値の可変サイズのまま、「次へ」を選択します。
<div align=center>
  <img src=image/CentOS7_strage.jpg>
</div>  

7. 仮想マシンのファイルを作成する場所とサイズを選択します。  
特に変更する必要が無ければそのまま作成を選択します。  
今回はサイズを40GBに変更しました。  
<div align=center>
  <img src=image/CentOS7_stragesize.jpg>
</div>  


#### VirtualBoxに先ほど作成した仮想マシンが表示されれば完了です。  

## 作成した仮想マシンのネットワークの設定

  先ほど作成した仮想マシンは、外部からの通信（SSH接続など）を受け付けないネットワークの設定になっています。  
  ここでは外部からの通信を受け付けるように設定を行います。  

  1. Virtualboxで先ほど作成した仮想マシンを選択し、「設定」を選択します。
  <div align=center>
    <img src=image/Virtualbox_config.JPG>
  </div>  

  2. 「ネットワーク」を選択すると、初期状態では「アダプター1」に「NAT」が設定されています。
  <div align=center>
    <img src=image/Virtualbox_network.JPG>
  </div>  

  3. 「アダプター1」を「NAT」から「ブリッジアダプター」、  
  「アダプター2」の「ネットワークアダプターを有効化」にチェックを入れ「ホストオンリーアダプター」を割り当てます。  
  <img src=image/Virtualbox_adapter1.JPG><img src=image/Virtualbox_adapter2.JPG>


## CentOS7のインストール

1. 先ほど作成した仮想マシン「Training」を選択し、右上の起動を選択します。
<div align=center>
  <img src=image/CentOS7_index.jpg>
</div>  

2. 起動ハードディスクを選択する画面が表示されるので、始めにインストールしたISOイメージファイルを選択し「起動」を選択します。
<div align=center>
  <img src=image/CentOS7_select.jpg>
</div>  

3. Install CentOS 7 にカーソルを合わせてEnterを押します。  

4. インストール時の言語を選択する画面が表示されるので、日本語を探し、選択します。  
<div align=center>
  <img src=image/CentOS7_lang.JPG>
</div>  

5. インストールの概要が表示されるので、設定されてないものを順に設定していきます。
<div align=center>
  <img src=image/CentOS7_custom.JPG>
</div>  

#### まずはインストールソースの設定をするので「インストールソース」を選択します。

6. デバイスの選択で表示されているハードディスクを選択しチェックマークが入ることを確認したら「完了」を選択します。
<div align=center>
  <img src=image/CentOS7_disc.JPG>
</div>  

#### 続いて「ソフトウェアの選択」を選択します。

7. 今回はよく使われる統合デスクトップ環境「GNOME Desktop」と、アドオンの「GNOMEアプリケーション」を選択し、  
完了を選択します。  
<div align=center>
  <img src=image/CentOS7_GUI.JPG>
</div>  

#### 次に「ネットワークとホスト名」を選択します。

8. 今回は「イーサネット（enp0s3）」をONにし、完了を選択します。  
（固定IPアドレスの設定は後でも行えます）
<div align=center>
  <img src=image/CentOS7_network.JPG>
</div>  

#### ここまでで黄色の△が消えるので、「インストールの開始」を選択します。

9. インストールが開始しますが、ここでユーザ情報の設定を行うよう促されます。  
<div align=center>
  <img src=image/CentOS7_user_custum.JPG>
</div>  

#### まずは「ROOTパスワード」を選択します。  
※ROOTパスワードとはrootユーザ（管理者ユーザ）のパスワードです。  
任意のパスワードを入力します。  
<div align=center>
  <img src=image/CentOS7_password.JPG>
</div>  
（複雑な文字列が理想ですが、短くても完了を二度押せば4文字くらいでも作成できます）

#### ROOTパスワードが作成できたら「ユーザの作成」を選択し、一般ユーザを作成します。
<div align=center>
  <img src=image/CentOS7_user_create.JPG>
</div>  
こちらも同じく単純なパスワードでも通ります。  
その後、インストールが終わるまでしばらく待ちます。

#### インストールが完了すると「再起動」を選択できるようになるので選択し、再起動します。  
<div align=center>
  <img src=image/CentOS7_install_finish.JPG>
</div>  

10.　再起動後、下のようなログイン画面が表示されるので、先ほど作成した一般ユーザのパスワードでログインをします。
<div align=center>
  <img src=image/CentOS7_login.JPG>
</div>  

11. ログイン後言語を選択する画面が表示されるので、「日本語」を選択します。
<div align=center>
  <img src=image/CentOS7_lang_select.JPG>
</div>  

12. オンラインアカウントに接続する画面が表示されるがスキップできる。

13. 設定完了後以下のような画面が表示されればCentOS7のインストールは完了です。
<div align=center>
  <img src=image/CentOS7_fin.JPG>
</div>  

# インストール後の設定

## ネットワークを有効にする
初期設定ではネットワークが無効となっているので設定をします。  
併せて、sudoコマンド（rootユーザにならなくても作業ができるコマンド）が使えるように設定も行います。  

1. 「全てのアプリケーション」から「お気に入り」の「端末」を選択しターミナルを実行します。
<div align=center>
  <img src=image/CentOS7_terminal.JPG>
</div>  

2. sudoの設定を行うため、`su -`を実行しrootユーザになります

3. rootユーザの状態で、`visudo`を実行します

4. 開いたファイルの最終行に、  
`【一般ユーザの名前】 ALL=(ALL) ALL`  
を追加します。  
※viとはエディタを表します。基本的な操作は https://net-newbie.com/linux/commands/vi.html を参照

5. `i`で編集を有効化し、`Esc`キーで編集を終了、 `:wq`を最後に入力しファイルを保存します。

6. その後rootユーザから抜けるため`exit`を入力します。

7. 一般ユーザに戻ったことを確認したら、  
`sudo vi /etc/sysconfig/network-scripts/ifcfg-enp0s3`  
を実行します。（一般ユーザのパスワードの入力を促されます）

8. `ONBOOT=no`という行を探し、`no`を`yes`に変更します。  
<div align=center>
  <img src=image/CentOS7_onboot2.JPG>
</div>  
※ONBOOTをyesにすることで起動時にenp0s3のネットワークが有効になります。  

9. 先ほどと同じように、`:wq`で保存をすると、ネットワークが有効になります。

## 作成した仮想マシンにSSHクライアントでアクセスする

### SSHとは

  「SSH」とは、暗号や認証の技術を利用して、安全に遠隔操作するコンピュータと通信するための方法です。  
  この認証を用いてサーバをリモートで操作できるのが「SSHクライアント」です。  
  Windows用の「SSHクライアント」には、  
　　　・Poderosa  
　　　・RLogin  
　　　・Tera Term  
  などがあります。  
  今回は「Tera Term」を利用してリモート操作を行ってみます。

### Tera Termのインストール
  1. 「Tera Term」は窓の杜（まどのもり）からダウンロードできます。以下URL  
  https://forest.watch.impress.co.jp/library/software/utf8teraterm/  

  2. 「Tera Term」の右にある「窓の杜からダウンロード」を選択し、ダウンロードします。  
  <div align=center>
    <img src=image/TeraTerm_download.JPG>
  </div>  

  3. ダウンロードしたexeファイルを実行し、手順に沿ってインストールを行います。  
  （初期値のままで支障はないので、すべてそのまま進めてOK）

### 接続先のIPアドレスを確認する

  「Tera Term」でSSH接続するには接続先のIPアドレスを確認し入力する必要があります。  
  IPアドレスを確認するには、CentOS上のターミナルで操作をします。

  1. CentOSでターミナルを起動します

  2. `ifconfig`を入力し、実行します。

  3. 接続先のネットワーク情報が表示されるので、「enp0s3」の「inet」を確認します。  
  「inet」に記載されているのがCentOSに設定されているIPアドレスです。

### Tera TermからSSH接続する

  1. Tera Termを起動します。  
  起動すると、下画像のような画面が表示されるので、先ほど確認したIPアドレスを「ホスト（T）」の部分に入力し「OK」を選択します。  
  <div align=center>
    <img src=image/TeraTerm_host.JPG>
  </div>  

  2. SSH認証画面が表示されるので、CentOSで作成したユーザの「ユーザ名」・「パスワード」を入力します。

  3. ログイン後以下のような状態になれば、接続成功です。
  <div align=center>
    <img src=image/TeraTerm_fin.JPG>
  </div>  

  4. 以降ほとんどの作業が「Tera term」で行えるようになります。
  （仮想マシンは起動しておく必要があります。）