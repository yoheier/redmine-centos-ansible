# redmine-centos-ansible

| :warning: インストールとadminでのログインは確認していますが、細かい動作は確認していません。細かいところが動かなかったらごめんなさい。 |
| --- |

最小構成でインストールしたCentOS8 Clone系OSにRedmine5 stableを自動インストールするためのAnsibleプレイブックです。

以下OSでの動作を確認しています
- CentOS8-stream
- Rocky Linux 8.6 (サポート終了予定日:2029年5月31日)
- AlmaLinux-8.7 (2022/11/21 boot.isoからインストールした最小構成で確認）

コマンド5個実行するだけで、あとはしばらく放置すればインストールが完了します。

ファーエンドテクノロジー株式会社さんのCentOS8用のプレイブックを泥臭く書き換えています。

## 概要

Ansibleを使ってRedmineを自動インストールするためのプレイブックです。以下のwebサイトで紹介されている手順におおむね準拠しています。

[Redmine 3.4をCentOS 7.3にインストールする手順](http://blog.redmine.jp/articles/3_4/install/centos/)

経緯等については私のqiita記事をご参照いただければ。
[Redmine5-stable系をCentOS8Clone系OSにインストールするAnsibleプレイブック作ってみた](https://qiita.com/yoheier/items/7df934c8afe0eabf5576)


## システム構成

* Redmine 5.0 Stable (from git)
* CentOS 8.0-stream or Rocky Linux 8.6 or ~~AlmaLinux-8.3 (8.6と8.7はNGです)~~ AlmaLinux-8.7（なんかうごいた）
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

 ```
 
**PostgreSQLとmysqlを選択できるようにプレイブックを変更しました**


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
~~AlmaLinuxもとりかかってみましたが、8.6 8.7とも、ミラーサーバのリポジトリがごちゃごちゃで、AppStreamの~~
~~メタ情報が間違っていたりします。~~
~~また、"Development tools"グループインストールに失敗するのでどうしたものやら、です。~~

~~8.3で何とか動きましたが、サポート期間とかどうなんでしょうねぇ……~~

**なんか公式リポジトリ見たら、8.6の整合性が取れてるようにみえたので時間があったら8.6での動確します(2022/11/18)**

**boot.isoから最小構成でインストールしたらうごきました。なぜだ。一応Adminでのログインとチケット作成は確認できたので大丈夫だろう的な。(2022/11/21)**

こだわりがなければRockyのほうがお勧めかもです。

## ライセンス

MIT License


## 作者

[ファーエンドテクノロジー株式会社](http://www.farend.co.jp/) and Fix added by yoheier(yohei-tamai@office-craft.co.jp)
