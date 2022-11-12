# redmine-centos-ansible

| :warning: インストールとadminでのログインは確認していますが、細かい動作は確認していません。細かいところが動かなかったらごめんなさい。 |
| --- |

最小構成でインストールしたCentOS8 Clone系OSにRedmine5 stableを自動インストールするためのAnsibleプレイブックです。

以下OSでの動作を確認しています
- CentOS8-stream
- Rocky Linux 8.6 (サポート終了予定日:2029年5月31日)

コマンド5個実行するだけで、あとはしばらく放置すればインストールが完了します。

ファーエンドテクノロジー株式会社さんのCentOS8用のプレイブックを泥臭く書き換えています。


## 概要

Ansibleを使ってRedmineを自動インストールするためのプレイブックです。以下のwebサイトで紹介されている手順におおむね準拠しています。

[Redmine 3.4をCentOS 7.3にインストールする手順](http://blog.redmine.jp/articles/3_4/install/centos/)

経緯等については私のqiita記事をご参照いただければ。
[Redmine4系をCentOS8にインストールするAnsible プレイブックを、Redmine5-stableに変更してCentOS8Clone系OSで動作するのを確認した](https://qiita.com/yoheier/items/7df934c8afe0eabf5576)


## システム構成

* Redmine 5.0 Stable (from git)
* CentOS 8.0-stream or Rocky Linux 8.6 or AlmaLinux-8.7
* PostgreSQL
* Apache


## Redmineのインストール手順

インストール直後の CentOS 8 stream に root でログインし以下の操作を行ってください。


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
ミラーリング設定を外して、蒸気サーバをalmalinux.repoを適当に編集してくださいｗ

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

### PostgreSQLに設定するパスワードの変更

ダウンロードしたプレイブック内のファイル `group_vars/redmine-servers` をエディタで開き、 `db_passwd_redmine` を適当な内容に変更してください。これはPostgreSQLのRedmine用ユーザー redmine に設定されるパスワードです。

### playbook実行

下記コマンドを実行してください。Redmineの自動インストールが開始されます。

```
cd redmine-centos-ansible
ansible-playbook -i hosts site.yml
```

10〜20分ほどでインストールが完了します。

webブラウザで `http://サーバIPアドレス/redmine` にアクセスしてください。Redmineの画面が表示されるはずです。


## ライセンス

MIT License


## 作者

[ファーエンドテクノロジー株式会社](http://www.farend.co.jp/) and Fix added by yoheier(yohei-tamai@office-craft.co.jp)
