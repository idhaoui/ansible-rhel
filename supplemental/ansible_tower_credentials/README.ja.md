# 演習 2.2 - インベントリー、認証情報、アドホックコマンド

**Read this in other languages**: ![uk](../../images/uk.png) [English](README.md),  ![japan](../../images/japan.png)[日本語](README.ja.md), ![brazil](../../images/brazil.png) [Portugues do Brasil](README.pt-br.md).

* [インベントリーの作成](#インベントリーの作成)
* [マシンの認証情報](#マシンの認証情報)
* [マシン認証情報の作成](#マシン認証情報の作成)
* [アドホックコマンドの実行](#アドホックコマンドの実行)
* [チャレンジラボ: アドホックコマンド](#チャレンジラボ-アドホックコマンド)

## インベントリーの作成

まず必要なのは、管理対象ホストの一覧です。これは Ansible Engine のインベントリーファイルに相当します。インベントリーにはダイナミックインベントリーなど、高度なものもありますが、まずは基本的なところから始めてみましょう。  

  - ブラウザで **https://student<X\>.<abcd\>.rhdemo.io** に接続しログインします。  
  <X\> <abcd\> に関しては環境ごとにユニークな値です。講師の指示に基づき入力してください。  
  ユーザーは `admin` パスワードは講師より指示があります。  

インベントリの作成は以下のように行います。  

  - 左側のWeb UIメニューで、**インベントリー** をクリックし、右端の ![plus](images/green_plus.png) ボタンをクリック。表示されるプルダウンの中から、インベントリーを選択します。  

  - **名前:** Workshop Inventory  

  - **組織:** Default  

  - **保存** をクリック  

ブラウザ下部に、利用可能なインベントリーが表示されていますので確認してみましょう。 Ansible Tower にあらかじめ作成されている **Demo Inventory** と、今作成した **Workshop Inventory** の2つが存在していることが分かります。ブラウザ上部に戻って、 **Workshop Inventory** の中にある **ホスト** ボタンをクリックすると、まだホストを 1 台も登録していないのでリストが空であることが分かります。  

早速ホストを追加してみましょう。まず、登録するためのホスト一覧を取得する必要があります。このラボ環境では、Tower がインストールされている ansible Control Host & Tower 上のインベントリーファイルに利用可能なホスト一覧が記述されていますのでそちらを確認してみます。  

Tower サーバーに SSH でログインします。  

> **注意**
>
> student\<X\> の \<X\> の部分は、各自与えられた Student 番号を入力ください。また、11.22.33.44の部分は、各自与えられた、ansible Control Hot & Tower の IP アドレスを入力ください。  

```bash
ssh student<X>@11.22.33.44
```

インベントリーファイルの場所は、Tower ホスト上の、 `~/lab_inventory/hosts` です。 cat で確認してみましょう。

```bash
$ cat ~/lab_inventory/hosts
[all:vars]
ansible_user=student<X>
ansible_ssh_pass=PASSWORD
ansible_port=22

[web]
node1 ansible_host=22.33.44.55
node2 ansible_host=33.44.55.66
node3 ansible_host=44.55.66.77

[control]
ansible ansible_host=11.22.33.44
```
> **注意**
>
> 表示される IP アドレスは各自固有のものになっていますので、上記とは異なります。  

node1、node2 などのホストの名前と IP addresses を控えておいてください。これらの情報を以下の手順で Tower のインベントリーに登録していきます。  

  - Tower のインベントリービューで **Workshop Inventory** をクリックします  

  -  **ホスト** をクリックします  

  -  ![plus](images/green_plus.png) ボタンをクリックします  

  - **ホスト名** `node1`  

  - **変数** 3つのハイフン `---` の下（2行目）に、 `ansible_host: 22.33.44.55` を入力します。 IP アドレス、`22.33.44.55` については、先ほど `node1` について確認した IP アドレスに置き換えてください。記述方法についても注意してください。コロン **:** と IP アドレスの間にはスペースが必要です。また、インベントリーファイルで利用するような **=** で記述してはいけません。  

  -  **保存** をクリックします  

  -  **ホスト** に戻って `node2` と `node3` を上記と同様の手順で追加します。  

これで Tower で管理する 3 台のホストに対するインベントリーファイルの作成が完了です?  

## マシンの認証情報

Ansible Tower は、ホストやクラウド等の認証情報をユーザーに開示せずに利用可能にするという優れた機能を持っています。 Tower がインベントリー登録したリモートホスト上でジョブを実行できるようにするには、リモートホストの認証情報を設定する必要があります。

> **メモ**
>
> これは Tower が持っている重要な機能の一つ **認証情報の分離の機能です**\! 認証情報はホストやインベントリーの設定とは別で強固に管理することが可能です。

Tower の重要な機能なので、まずはコマンドラインでラボ環境について確認してみましょう。

SSH 経由で Tower ホストにログインします。

  - Tower コントローラーへ SSH でログインします: `ssh student<X>@student<X>.workshopname.rhdemo.io`
  - \<X\> の部分と、workshopname は各自のものに置き換えてください。

  - `node1` に SSH 接続し、 `sudo -i` を実行してみます。 SSH 接続の際はパスワード入力が要求されますが、 `sudo -i` に関してはパスワードは要求されません。

```bash
[student<X>@ansible ~]$ ssh student<X>@22.33.44.55
student<X>@22.33.44.55's password:
Last login: Thu Jul  4 14:47:04 2019 from 11.22.33.44
[student<X>@node1 ~]$ sudo -i
[root@node1 ~]#
```

これはどういう意味でしょう？  

  - **student<X\>** は、インベントリーで指定した管理対象ホストにパスワードベースの SSH で接続可能  

  - **student<X\>** は、 `sudo`  による権限昇格で、**root** としてコマンド実行が可能  

## マシン認証情報の作成  

では早速 Tower で認証情報を作成してみましょう。 Tower　左側のメニューから **認証情報** をクリックします。  

 ![plus](images/green_plus.png) ボタンをクリックします。  

  - **名前:** Workshop Credentials  

  - **組織:** Default  

  - **認証情報タイプ:** 虫眼鏡をクリックして **マシン** を選択し、**選択**  をクリックします  

  - **ユーザー名:** student\<X\> - いつもの通り、 **\<X\>** は各自に割り当てられた番号を入力ください  

  - **パスワード:** 講師から指示があります。

  - **保存** をクリックします

  - パスワード部分が暗号化され表示されないことを確認します

> **ヒント**
>
> Tower では、入力フィールドの横に虫眼鏡アイコンが表示されているときにはクリックすると選択リストが開きます。  

これで、後でインベントリーホストに使用する資格情報を設定できました。

## アドホックコマンドの実行

Ansible Engine で実行したアドホックコマンドを Tower でも実行してみましょう。

  - 左の選択メニューから、**インベントリー** → **Workshop Inventory**

  - **ホスト** ボタンをクリックし、登録したホスト一覧を表示し、`オン` の左にあるチェックボックスを全てのホストでチェックします  

  - **コマンドの実行** をクリックします。さらに次の画面でアドホックコマンドを下記の通り指定していきます。  

      - **モジュール** 欄で **ping** を選択  

      - **マシンの認証情報** 欄で **Workshop Credentials** を選択  

      - **起動** をクリックし、出力を確認します  

シンプルな **ping** モジュールにはオプションは必要ありません。しかしながら、他のモジュールの場合、実行には引数が必要な場合があります。次にこのケースを確認してみましょう。先ほどと同様の手順で、今度は **command** モジュールを使って実行中のユーザーのユーザー ID を確認してみます。  

- **モジュール:** command  

- **引数:** id  

マシンの認証情報も入力して実行の上表示内容を確認します。  

> **ヒント**  
>
> 「引数」の横にある疑問符をクリックすると、モジュールのドキュメントページへのリンクが表示されます。とっても便利ですね。♪  

次に、/etc/shadow のファイルの中身を確認するコマンドを発行してみましょう  

- **モジュール:** command  

- **引数:** cat /etc/shadow  

> **注意**  
>
> **エラーが発生ます**  

失敗します、何故でしょう？  

理由は、そう、権限昇格が必要なのです。もう一度戻って、 **権限昇格の有効化** にチェックを入れて実行してみてください。  

今回はうまくいきました。 root 権限で実行する必要があるタスクの場合、権限昇格を有効化する必要があります。これは、 Playbook  の記述でもよく見かける　**become: yes**  と同じです。  

## チャレンジラボ: アドホックコマンド  

アドホックコマンドを使って、node1, node2 node3 にパッケージ「tmux」をインストールしてください。  

> **ヒント**  
>
> **yum** モジュールを利用します。使い方は `[ansible@tower ~]$ ansible-doc yum` などで確認してみてください。  

> **回答**  

  - **モジュール:** yum  

  - **引数:** name=tmux  

  - **権限昇格の有効化:** チェックします  

> **ヒント**
>
> コマンドの黄色の出力は、Ansibleが実際に何かを実行したことを示しています（ここでは、パッケージがインストールされてなかったのでインストールを実行しました）。アドホックコマンドを再度実行すると、出力が緑色になり、パッケージが既にインストールされていることが通知されます。Ansible   の黄色は「注意」を意味するわけではありません…;-)。ご注意を…? ;-).

----

[Ansible Tower ワークショップ表紙に戻る](../../README.ja.md#section-2---ansible-towerの演習)
