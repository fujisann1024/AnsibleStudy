# 前提

- インストール(初回)

```shell
wsl --install -d Ubuntu-24.04 --name Ubuntu2404-Base
```

- 起動

```shell
wsl -d Ubuntu2404-Base
```

- 停止

```shell
exit
```

- パッケージを最新の状態に更新

```shell
sudo apt update
sudo -y apt upgrade
```

## サーバーの構築準備

- [Ansible のインストール](https://docs.ansible.com/projects/ansible/latest/installation_guide/installation_distros.html?utm_source=chatgpt.com#installing-ansible-on-ubuntu)

```shell
sudo apt install software-properties-common
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install ansible

which ansible
ansible --version

sudo apt install -y acl
which setfacl
setfacl --version
```

Ubuntu 標準の APT リポジトリより最新の Ansible をインストールできる

→Ubuntu は LTS にはむやみに新機能を入れない方針のため

## ディレクトリ

```
├─ansible.cfg
├─requirements.yml ・・・
├─inventories
│  └─group_vars
├─playbooks
└─roles
    └─postgresql
        ├─defaults・・・Roleのデフォルト変数を定義
        ├─handlers・・・notify によって呼び出される処理
        ├─meta・・・Roleの作者情報や、他のRoleへの依存関係を定義
        ├─tasks・・・実行するタスク（手順）を記述
        └─templates・・・template モジュールで使う .j2 ファイル（変数埋め込み用）を置く
```

## 実行方法

ファイルのコピー

```shell
cd /home
sudo cp -r /mnt/d/Project/Ansible/ansible /home
cd ansible/todo/
```

コレクション導入

```shell
sudo ansible-galaxy collection install -r requirements.yml
ansible-galaxy collection list | grep community.postgresql
```

プレイブック実行

```shell
ansible-playbook playbooks/reset.yml -K
ansible-playbook playbooks/site.yml -K
```

応急処置

```shell
# パスワードの設定
sudo -u postgres psql
ALTER USER postgres WITH PASSWORD 'postgres';
\q

# warファイルの配置
sudo cp artifacts/app.war /var/lib/tomcat10/webapps/app.war

#tomcatの再起動
sudo systemctl restart tomcat10
```

-- DB 確認

```

systemctl status postgresql --no-pager
systemctl status tomcat10 --no-pager
systemctl status apache2 --no-pager

sudo -u postgres psql -c "SELECT version();"
sudo -u postgres psql -c "\du"
sudo -u postgres psql -c "\l"

curl -I http://localhost/
curl -I http://localhost/app/
```
