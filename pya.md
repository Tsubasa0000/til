# 二橋

```
これから前回の中間発表で 「Ansible なんもわからん」
から改名した 「Ansibleﾁｮｯﾄﾃﾞｷﾙ」 の発表をはじめます。
```

(紹介)

```
メンバーは、中山、二橋、朴の3人です。
```

(紹介->研究概要)

```
研究概要は VMware ESXi 上に展開した Linux を
RedHat 社が開発している構成管理ツール「Ansible」を用いて
サーバーを自動構築する というものです。
```

(紹介->研究背景)

```
サーバ構築での設定をしていく作業が手間がかかると思い、
自動でできたらいいな、便利だなと思い、最近流行っている
Ansibleを用いて自動構築をやってみることにしました。
```

(構成図)

```
構成図はこのようになっております。
サーバに仮想化基盤であるVMware ESXiをインストールしました。
ESXiを使うと、複数の仮想的なコンピューターを作ることができます。

複数の仮想マシンを作成し、Windows Server や Linux 
をインストールしました。

なお、ESXiでは内部に仮想スイッチが実装されており、仮想マシン上のNICに接続されていて、
仮想マシン間で相互通信することができます。
```

(構成図->テーブル)

```
今回は、ホスト名でも名前解決ができるように、Windows Server を用いて、DNS サーバを作成しました。
命名規則は hogehoge.ansible.esxi71.local としました。
```

(Ansible)

```
次に、Ansibleについての説明です。

左の図のように、一つ一つ手作業で複数のサーバの構築を行った場合、作業者の作業ミスや
より多くの時間や、コストがかかってしまいます。

Ansible などの構成管理ツールを使用した場合は、あらかじめ設定を定義したファイルを記述することで、
複数のサーバに対して一度に設定をかけることができます。

これを用いた場合は、作業ミスが減り、品質向上、時間やコスト削減が見込まれます。
```

(Ansible->特徴)

```
次に、Ansibleの特徴です。

Ansibleは、次の4つの特徴があります。

・シンプル
可読性が高いYAMLというデータ記法で記述されます。YAMLはインデントと改行に意味を持ちますが、
それだけを理解すればすぐにどのような処理を行っているのかが分かります。

・パワフル
Ansibleは、マルチベンダーでサポートしており、ネットワーク機器やオペレーティングシステム、
クラウドまで、あらゆる製品をコントロールすることが出来ます。

・エージェントレス
Ansibleは、ターゲットのサーバにAnsibleやエージェントソフトウエアをインストールする必要がありません。
AnsibleはSSHを利用して、安全に送信、実行することが出来ます。

・最後にべき等性です。
同一の設定を何度繰り返し行っても、一度行った操作は再度実行しても設定が変更されない。という概念です。
これが実装されていなければ、既にインストールされたソフトウェアであっても、
既存の設定を上書きされてしまう可能性があります。これを防ぐためにべき等性というものが担保されています。
```

(Ansible->Inventory)

```
次に Inventory ファイルについてです。
インベントリとは、自動化するターゲットホストを定義をするファイルです。

この左の図では、「web_servers」というグループにはnode01 node02 node03 のホストが所属していて
「mysql」というグループには mysql01 mysql02 というホストが所属しています。
```

(Ansible->Playbook)

```
次はPlaybookです。
Playbookとは、環境構築に関する設定情報を定義したファイルのことです。
先程も述べた通り、YAMLという言語で記述するため、コーディング経験が少ない方でも記述が簡単に行えます。

この左の図では、「web_servers」というホストに対して、firewalld を停止、
自動起動を無効化するという設定情報が定義されています。
```

(Ansible->Module)

```
次にモジュールです。
モジュールは、今まで手作業やスクリプトで実行していたものを機能単位でまとめたものです。
このモジュールをプレイブックで定義することで、処理を対象のホストに実行することができます。
ほかにも何千個とモジュールがありますが、その中からいくつか紹介します。

モジュールはサーバ系、クラウド系、ネットワーク系に分類されていて、
サーバ系だとyum systemd　など
クラウド系だと AWS Azure　など
ネットワーク系だと Cisco IOS や Juniper Network の Junos（じゅのす）　へのモジュールがあります。
```
(構築)


# 喋る人変更－＞朴

(構築)

```
次に、Ansibleの構築手順を説明します。
まず、AnsibleはEPEL(イーペル)というリポジトリに登録されているため、
yum install epel-release コマンドを実行して、epelをインストールします。
```

(構築->インストール)

```
EPELリポジトリをインストールしたのでAnsible を 
yum install ansible コマンドでインストールします。
```

(構築->インベントリの作成)

```
次は、Ansibleの土台となるインベントリファイルを作成します。

今回は、CentOSは RedHat 、Ubuntu は Ubuntu というグループを設定しました。
また、RedHat、Ubuntu 2つのグループをまとめたものを node というグループに設定しました。
```

(構築->PlayBookの作成)

```
次は、Playbookの作成です。可読性を高めるためにロール機能を使って各設定ファイルを階層化しています
Linuxの初期設定作業は、common ロールというものを利用してタスクを管理しています。
```

(構築->Playbookの作成1)

```
実際に作成したPlaybookを何個か説明していきます。
これはFirewallの設定を記述したファイルです。

RedHedとDebianで設定内容が異なるので、ansible_os_familyという変数を使うことで
ディストリビューション毎の設定に合わせて記述しています。
```

(構築->Playbookの作成2)

```
これは、パッケージのインストールについて記述したファイルです。
これもRedHedとDebianでパッケージ管理システムやパッケージ名が異なるので、
ディストリビューションごとの設定を記述しています。
```

(構築->Playbookの作成3)

```
これは、みんな大嫌いなSELinuxの無効化について記述したファイルです。
SELinuxを有効にしていると正常に反映されないことがあったので、今回は無効にしました。

本来は有効にするべきですが、管理者権限の操作をSELinuxで制限されてしまうので、
検証や設定・構築する上で設定を反映できなくなってしまうことがあるので
少なくとも検証環境では無効にしてよいと考えます。
```

(結果)

# しゃべる人変更－＞中山

(結果)

```
実行した結果がこちらです。
実行後に最終的な実行結果が出力されます。

OKはべき等性が働いて処理をしなかった。
CHANGEDは指定したステータスになっていないので変更かけた。
FAILEDは何らかのエラーが発生した。
SKIPPEDは条件に当てはまらなかったので処理をしなかった。

など、結果が表示されました。
```

(結果->Firewall)

```
次に実際に設定を適用した結果を確認します。

左の図がFirewallのPlaybookで、右の図が実際に確認した画面です。
CentOS、Ubuntuどちらも起動・自動起動が有効になっているのが分かります。
```

(結果->Packages)

```
次にパッケージの動作についてです。
左の図がパッケージのPlaybookで、右の図が実際に確認した画面です。
上の結果がCentOSの結果で、下の結果がUbuntuの結果です。
それぞれPlaybookに指定した通りのパッケージがインストールされていることが分かります。
```

(結果->SELinux)

```
次にSELinuxの無効化についてです。
左の図がSELinuxのPlaybookで、右の図が実際に確認した画面です。
SELinuxはRedHad系のOSではデフォルトで有効になっているので、
node01 node02 のみに設定を適用しています。

/etc/sysconfig/selinux を見てみると、disabled になっていることが分かります。
```

(結果->Apache)

```
最後にApacheのBasic認証についてです。
左側の図がApacheのBasic認証について記述したファイルです。
このPlaybookを実行すると、Basic認証が適用されたページが生成されます。

右の図では、Basic認証を求める認証ダイアログが表示され、
ログインをするとページが表示されているのが分かると思います。
```

(結果->Apacheテストサーバ)

```
実際に4つのノードにBasic認証を適用したサイトを用意しました。
よかったら、QRコードで読み取ってみてください。

ユーザ名は ccuser パスワードは Cnetuser で入れます。
```

(15秒くらいまつ)

```
サイトにアクセスすると、認証ダイアログが表示され、認証がかかっているのが分かると思います。
```

(結果->デモンストレーション)

```
これから4つのノードに環境構築している様子をお見せします。
```

(1回目拡大)

```
最初にお見せするのは、1回目の実行時の結果です。
今回は先程お見せしたPlaybook以外にも、時間の都合上 省略した
ユーザ作成、NTP設定 ロケール設定 リゾルバの設定 もまとめて実行しています。

左下は、Linuxのコンソール画面です。再起動時に画面が変化します。

ok になっているものや changed 、skipped になっているのが分かると思います。
もうすぐ、再起動がかかります。
```
(25秒くらいにして喋る)

```
ansibleによって、自動的に再起動がかかったのが分かると思います。
すこし、飛ばします。
```

(1:05まで飛ばす)

```
画面が戻ると、また処理が再開するのが分かると思います。

1回目の実行結果では、ok や 設定が適用されたという意味である
chenged のカウントが増えているのが分かります。
```

(2回目 拡大)

```
次に、2回目の実行時の結果です。
```

(5秒くらいたったら喋る)

```
さっき黄色になっていたところが緑色になっているのが分かります。
2回目の実行結果では、同一の設定を行ったので設定が変更されずに、
すべてokになり、べき等性が働いていることが確認できました。
```

(まとめ)

```
まとめです。
・本研究では、Ansibleを使用して複数のディストリビューションごとの設定を
　一度にサーバ構築を行うことができた。

・最初は何も分からなかったが、本や参考サイトを参考にすることで完全に理解することが出来ました。
・この研究を通して、システム構築をする際の大幅な時間短縮をすることが出来ました。
　なので、100台200台くらいの大規模なシステムでも簡単に行うことができると考えます。
```

(まとめ->質疑応答)


```
(発表は以上です。何か質問のある方いらっしゃいますか)

これでグループ18 Ansibleﾁｮｯﾄﾃﾞｷﾙ から改名して、Ansible完全に理解した の発表をおわります。
ご静聴いただきありがとうございました。
```
