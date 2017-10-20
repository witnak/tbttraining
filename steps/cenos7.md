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
<div align=center>
  <img src=image/CentOS7_size.jpg>
</div>  
今回は1024MBのまま、「次へ」を選択します。  

4. ハードディスクの作成方法を選択します。  
<div align=center>
  <img src=image/CentOS7_harddisc.jpg>
</div>  
新規で作成する場合は、仮想ハードディスクを作成する（C）にチェックを入れ、「作成」を選択します。  

5. ハードディスクのファイルタイプを選択します。  
<div align=center>
  <img src=image/CentOS7_type.jpg>
</div>  
今回はVMDKにチェックを入れ「作成」を選択します。  

6. ハードディスクにあるストレージの設定を行います。
<div align=center>
  <img src=image/CentOS7_strage.jpg>
</div>  
初期値の可変サイズのまま、「次へ」を選択します。

7. 仮想マシンのファイルを作成する場所とサイズを選択します。  
<div align=center>
  <img src=image/CentOS7_stragesize.jpg>
</div>  
特に変更する必要が無ければそのまま作成を選択します。  
今回はサイズを40GBに変更しました。

#### VirtualBoxに先ほど作成した仮想マシンが表示されれば完了です。  

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
  <img src=image/CentOS7_lang.jpg>
</div>  

5. インストールの概要が表示されるので、設定されてないものを順に設定していきます。
<div align=center>
  <img src=image/CentOS7_custom.jpg>
</div>  
まずはインストールソースの設定をするので「インストールソース」を選択します。

6. デバイスの選択で表示されているハードディスクを選択しチェックマークが入ることを確認したら「完了」を選択します。
<div align=center>
  <img src=image/CentOS7_disc.jpg>
</div>  
続いて「ソフトウェアの選択」を選択します。

7. 今回はよく使われる統合デスクトップ環境「GNOME Desktop」と、アドオンの「GNOMEアプリケーション」を選択し、  
完了を選択します。  
<div align=center>
  <img src=image/CentOS7_GUI.jpg>
</div>  
次に「ネットワークとホスト名」を選択します。

8. 今回は「イーサネット（enp0s3）」をONにし、完了を選択します。  
（固定IPアドレスの設定は後でも行えます）
<div align=center>
  <img src=image/CentOS7_network.jpg>
</div>  
ここまでで黄色の△が消えるので、「インストールの開始」を選択します。

9. インストールが開始しますが、ここでユーザ情報の設定を行うよう促されます。  
<div align=center>
  <img src=image/CentOS7_custum.jpg>
</div>  
まずは「ROOTパスワード」を選択します。  
※ROOTパスワードとはrootユーザ（管理者ユーザ）のパスワードです。  
任意のパスワードを入力します。  
<div align=center>
  <img src=image/CentOS7_password.jpg>
</div>  
（複雑な文字列が理想ですが、短くても完了を二度押せば4文字くらいでも作成できます）
ROOTパスワードが作成できたら「ユーザの作成」を選択し、一般ユーザを作成します。
<div align=center>
  <img src=image/CentOS7_user_create.jpg>
</div>  
こちらも同じく単純なパスワードでも通ります。  
その後、インストールが終わるまでしばらく待ちます。

インストールが完了すると「再起動」を選択できるようになるので選択し、再起動します。
<div align=center>
  <img src=image/CentOS7_install_finish.jpg>
</div>  

10.　再起動後、下のようなログイン画面が表示されるので、先ほど作成した一般ユーザのパスワードでログインをします。
<div align=center>
  <img src=image/CentOS7_user_login.jpg>
</div>  

11. ログイン後言語を選択する画面が表示されるので、「日本語」を選択します。
<div align=center>
  <img src=image/CentOS7_lang_select.jpg>
</div>  

12. オンラインアカウントに接続する画面が表示されるがスキップできる。

13. 設定完了後以下のような画面が表示されればCentOS7のインストールは完了です。
<div align=center>
  <img src=image/CentOS7_fin.jpg>
</div>  