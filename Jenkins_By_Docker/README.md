## 要件定義
GitHubのmaster-branchにpushしたら、jenkinsに通知が行きjenkinsはBackupホストに接続し、GitHubのBackupが作成されるようにする。

## 手順

1. コンテナ作成  
```
# docker build -t jenkins .
# docker run --privileged -d -p 8080:8080 --name jenkins jenkins /sbin/init 
# docker exec -it jenkins cat /var/lib/jenkins/secrets/initialAdminPassword
```
※privilegedオプション、起動シェルなどはsystemctlでjenkinsを起動する際にエラーが出てしまうため(調査中)。

2. Ultrahookインストール  
Ultrahookはオンプレにインストールする。
```
# yum -y install ruby ruby-devel gcc
# gem install ultrahook
```
