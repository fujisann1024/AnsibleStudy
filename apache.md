環境構築(手動)

```shell
# Apache インストール
sudo apt install -y apache2

# docroot 作成
sudo mkdir -p /var/www/app/static
sudo chown -R www-data:www-data /var/www/app
sudo chmod -R 755 /var/www/app

# 必要モジュール有効化
sudo a2enmod proxy proxy_http headers rewrite

# vhost 設定ファイル作成
cat <<'EOF' | sudo tee /etc/apache2/sites-available/app.conf
<VirtualHost *:80>
    ServerName localhost

    DocumentRoot /var/www/app

    <Directory /var/www/app>
        Options -Indexes +FollowSymLinks
        AllowOverride None
        Require all granted
    </Directory>

    Alias /static/ /var/www/app/static/
    <Directory /var/www/app/static/>
        Require all granted
    </Directory>

    ProxyPreserveHost On
    ProxyPass        /app/  http://127.0.0.1:8080/
    ProxyPassReverse /app/  http://127.0.0.1:8080/

    ErrorLog  ${APACHE_LOG_DIR}/app_error.log
    CustomLog ${APACHE_LOG_DIR}/app_access.log combined
</VirtualHost>
EOF

# 有効化
sudo a2ensite app.conf
sudo a2dissite 000-default.conf
sudo apachectl configtest


# Apache 起動・反映
sudo systemctl enable --now apache2
sudo systemctl reload apache2
sudo systemctl status apache2 --no-pager

```
