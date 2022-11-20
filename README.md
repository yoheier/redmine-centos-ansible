# redmine-centos-ansible

| :warning: インストールとadminでのログインは確認していますが、細かい動作は確認していません。細かいところが動かなかったらごめんなさい。 |
| --- |

最小構成でインストールしたCentOS8 Clone系OSにRedmine5 stableを自動インストールするためのAnsibleプレイブックです。

以下OSでの動作を確認しています
- CentOS8-stream
- Rocky Linux 8.6 (サポート終了予定日:2029年5月31日)
- AlmaLinux-8.3

コマンド5個実行するだけで、あとはしばらく放置すればインストールが完了します。

ファーエンドテクノロジー株式会社さんのCentOS8用のプレイブックを泥臭く書き換えています。

## 概要

Ansibleを使ってRedmineを自動インストールするためのプレイブックです。以下のwebサイトで紹介されている手順におおむね準拠しています。

[Redmine 3.4をCentOS 7.3にインストールする手順](http://blog.redmine.jp/articles/3_4/install/centos/)

経緯等については私のqiita記事をご参照いただければ。
[Redmine5-stable系をCentOS8Clone系OSにインストールするAnsibleプレイブック作ってみた](https://qiita.com/yoheier/items/7df934c8afe0eabf5576)


## システム構成

* Redmine 5.0 Stable (from git)
* CentOS 8.0-stream or Rocky Linux 8.6 or AlmaLinux-8.3 (8.6と8.7はNGです)
* PostgreSQL or mysql(madiaDB)
* Apache

**PostgreSQLとmysqlを選択できるようにプレイブックを変更しました**


## Redmineのインストール手順

インストール直後の OSに root でログインし以下の操作を行ってください。


### Ansibleとgitのインストール

```
yum update -y
yum install -y epel-release glibc-locale-source
yum install -y ansible git

===================== Dockerの場合は以下も実行する===========================
yum install -y policycoreutils selinux-policy-targeted firewalld sudo
===========================================================================

================== AlmaLinux8.3の場合は上記ではなく下記インストールを行う======
リポジトリ設定について、ほとんどの（全ての？）ミラーサーバは8.6以降の情報しか
持っていないようですので、
8.3は、AlmaLinux公式のサーバから取得するしかないようです。
ミラーリング設定を外して、公式サーバをalmalinux.repoを適当に編集してくださいｗ

githubにALmaLinux8.3の検証に使った際のyumリポジトリ定義を入れてありますので参考にしていただければ。
下記ファイルを編集です。

/etc/yum.repo.d/almalinux.repo

https://repo.almalinux.org/vault/8.3/BaseOS/x86_64/os/

https://repo.almalinux.org/vault/8.3/AppStream/x86_64/os/

dnf update -y(行うとなぜかバージョンが上がるのでしないほうがいいかも)

dnf install -y epel-release glibc-locale-source
dnf install -y git

dnf install -y python38
pip3 install setuptools-rust wheel
pip3 install --upgrade pip
python3 -m pip install ansible
===========================================================================
```

Rocky LinuxとAlmaLinuxの場合、大抵カーネルの更新が入ると思いますので、rebootしてからplaybookを実行してください。

### playbookのダウンロード

```
git clone https://github.com/yoheier/redmine-centos-ansible.git
```

### Databaseに設定するパスワードの変更

ダウンロードしたプレイブック内のファイル `group_vars/redmine_servers` をエディタで開き、 `db_passwd_redmine` を適当な内容に変更してください。これはDatabaseのRedmine用ユーザー redmine に設定されるパスワードです。

### Databaseの選択

下記の用にデフォルトではmysql(mariaDB)が選択されています。
PostgreSQLで入れる場合はコメントを切り替えてください。

```
# Redmineで使用するDatabase
#redmine_db: postgres
redmine_db: mysql
```

### playbook実行

下記コマンドを実行してください。Redmineの自動インストールが開始されます。

```
cd redmine-centos-ansible
ansible-playbook -i hosts site.yml
```

10〜20分ほどでインストールが完了します。

webブラウザで `http://サーバIPアドレス/redmine` にアクセスしてください。Redmineの画面が表示されるはずです。

## 言い訳
AlmaLinuxもとりかかってみましたが、8.6 8.7とも、ミラーサーバのリポジトリがごちゃごちゃで、AppStreamの
メタ情報が間違っていたりします。
また、"Development tools"グループインストールに失敗するのでどうしたものやら、です。

8.3で何とか動きましたが、サポート期間とかどうなんでしょうねぇ……

**なんか公式リポジトリ見たら、8.6の整合性が取れてるようにみえたので時間があったら8.6での動確します(2022/11/18)**

こだわりがなければRockyのほうがお勧めかもです。

## ライセンス

MIT License


## 作者

[ファーエンドテクノロジー株式会社](http://www.farend.co.jp/) and Fix added by yoheier(yohei-tamai@office-craft.co.jp)
