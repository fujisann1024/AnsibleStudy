環境構築(手動)

```shell
# インストール
sudo apt install -y postgresql-16 postgresql-client-16 python3-psycopg2 acl

# サービス起動・自動起動
sudo systemctl enable --now postgresql
sudo systemctl status postgresql --no-pager

# クラスタ状態確認
sudo pg_lsclusters

# listen_addresses を conf.d で設定
sudo mkdir -p /etc/postgresql/16/main/conf.d
cat <<'EOF' | sudo tee /etc/postgresql/16/main/conf.d/99-ansible.conf
# Managed manually (Ansible equivalent)
listen_addresses = 'localhost'
EOF

# リモート許可にする
sudo sed -i "s/listen_addresses = 'localhost'/listen_addresses = '*'/" /etc/postgresql/16/main/conf.d/99-ansible.conf


# 反映
sudo systemctl restart postgresql

# DBユーザー/DB作成
sudo -u postgres psql -c "CREATE USER appuser WITH PASSWORD 'AppUserPassword_ChangeMe';"
# sudo -u postgres psql -c "CREATE DATABASE appdb OWNER appuser;"

sudo -u postgres psql -c "\du"
sudo -u postgres psql -c "\l"

```

```shell
sudo -u postgres psql -d STUDY_DB
```

※-h localhost を指定するとネットワーク経由の接続（TCP/IP）になり、パスワード設定が必要になる
