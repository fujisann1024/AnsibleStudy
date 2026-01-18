```shell
# Java と Tomcat のインストール
sudo apt install -y openjdk-21-jdk tomcat10

# Tomcat 起動・自動起動
sudo systemctl enable --now tomcat10
sudo systemctl status tomcat10 --no-pager

# setenv.sh（JAVA_OPTS）設定
cat <<'EOF' | sudo tee /usr/share/tomcat10/bin/setenv.sh
#!/bin/sh
# Managed manually (Ansible equivalent)
export JAVA_OPTS="-Xms256m -Xmx512m"
EOF
sudo chmod 755 /usr/share/tomcat10/bin/setenv.sh
sudo systemctl restart tomcat10

# WARファイルを webapps に配置してデプロイ
sudo rm -rf /var/lib/tomcat10/webapps/app
sudo cp artifacts/app.war /var/lib/tomcat10/webapps/app.war
sudo chmod 644 /var/lib/tomcat10/webapps/app.war
sudo systemctl restart tomcat10

# 動作確認
curl -I http://localhost:8080/app/

```
