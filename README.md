# centos + nginx + php-fpm×n個をアクセス元ipで振り分ける  
# dockerをオフラインで使う  
centos7(minimal)  
他のバージョンはdocker-compose参照  
  
# ■インストール関連  
  
## windowsでの操作  
以下でダウンロードしたファイルを移す（バージョンはできる限りローカルに合わせる）  
https://download.docker.com/linux/static/stable/x86_64/docker-19.03.6.tgz  
https://github.com/docker/compose/releases/download/1.25.4/docker-compose-Linux-x86_64  
  
※centosのsyopユーザhomeディレクトリにdownload_instディレクトリを作成  
### アップロード  
scp -r ./docker-19.03.6.tgz sysop@{centos}:/home/sysop/download_inst  
scp -r ./docker-compose-Linux-x86_64 sysop@{centos}:/home/sysop/download_inst  
  
#centosでの操作（sysop）  
## dockerの展開  
cd 転送先のディレクトリ  
tar zxvf docker-17.09.1-ce.tgz  
sudo cp docker/* /usr/bin  
sudo dockerd &  
ps -ef | grep [d]ocker  
  
## composeの展開  
sudo cp docker-compose-Linux-x86_64 /usr/bin/docker-compose  
sudo chmod +x /usr/bin/docker-compose  
docker-compose -v  
  
sudo groupadd docker # dockerグループを作成  
sudo gpasswd docker # ここでグループパスワードを設定  
  
## 再起動後  
以下を /etc/systemd/system/　へ配置  
  
sudo vi /etc/systemd/system/docker.service  
※setup1/docker.service 参照  
  
sudo systemctl restart docker # dockerをリスタート  
  
### sysopユーザがdockerコマンドを打つときは最初に  
$sysop  
newgrp docker  
を打つこと。  
  
# docker操作  
## 開発環境でのイメージ出力  
docker save nginx:1.16.1 > nginx_img  
  
## 本番環境へのイメージロード  
docker load < nginx_img  
  
## 起動方法  
docker-compose up -d  
  
## エラー時のトラブル  
・docker-compose時にディレクトリ構成が一致しないとOCIエラー  
・一致してるのにOCIエラー　→selinux関連。offにして解決。  
　vi /etc/selinux/config  
(以下にパラメータを変更し、reboot)  
SELINUX=disabled  # enforcingからdisabledに変更  
TODO:selinux切らずに使う方法考える  
