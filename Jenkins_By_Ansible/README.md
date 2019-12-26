## 要件定義

1. GitHubのmasterブランチにプッシュする
2. GitHubからJenkins(AWS)に通知が飛ぶ
3. JenkinsからGitHubバックアップサーバに接続しpullする
4. CoudFormation/ansibleを使用して、自動で上記環境を構築する(将来)

### Input情報

- GitHubバックアップサーバIP  
- GitHubバックアップサーバPort  
- 接続用秘密鍵配置(必要なら)

## Jenkins

1. Jenkins構築  
2. APIトークン  
3. ジョブ  

	1. ビルドのパラメータ化(ブランチ対応)
	2. ビルド/トリガ(トークン名)
	3. ビルド(シェルの実行)
```
例：

#!/bin/bash -xe
ssh-keyscan -t rsa 【バックアップホストIP】 >> ~/.ssh/known_hosts
ssh -i /var/lib/jenkins/.ssh/private.pem centos@【バックアップホストIP】 "cd /home/centos/work; if [[ ! -d /home/centos/work/【リポジトリ名】 ]]; then mkdir 【リポジトリ名】; cd 【リポジトリ名】; git init; fi; cd 【リポジトリ名】; git pull https://github.com/【GitHubユーザ名】/【リポジトリ名】.git"
```

- Expression:.*"ref":"refs\/heads\/master".*
- Label:${ENV,var="payload"}
4. CSRF無効化  
Jenkins -> Jenkinsの管理 -> グローバルセキュリティの設定 -> CSRF対策

5. プラグイン  

	1. Github Plugin  
推奨インストールで標準搭載。
	2. Conditional Step  
条件分岐によるビルド(ブランチ毎)。

6. SSH Key問題

- jenkinsユーザにてGitHubバックアップサーバに接続しknown_hostsに登録しないとエラー  
- 秘密鍵配置  
```
/var/lib/jenkins/.ssh/【秘密鍵】

# chown -R jenkins:jenkins /var/lib/jenkins/.ssh
```

## GitHub
#### Webhook設定    
ペイロードURL設定
```
http://【jenkinsユーザ名】:【apiトークン】@【任意】.namespace.ultrahook.com/?token=【apiトークン名】
※↑では複数ジョブによる複数URLへのフォワーディングに対応出来ないので下記に変更！

http://【jenkinsユーザ名】:【apiトークン】@【任意】.namespace.ultrahook.com/job/【ジョブ名】/buildWithParameters?token=【apiトークン名】

ちなみに大本のペイロードURLは下記
http://[JENKINS_USERID]:[API_TOKEN]@[JENKINS_HOST]/job/[JOB_NAME]/buildWithParameters?token=[TOKEN_NAME]
```

## Ultrahook
トークン登録
```
$ echo "api_key: {APIKEY}" > ~/.ultrahook
```
フォワーディングURL設定
```
$ ultrahook 【任意】 http://【jenkinsホストIP:ポート番号】/job/【ジョブ名】/buildWithParameters
※↑では複数ジョブによる複数URLへのフォワーディングに対応出来ないので下記に変更！

$ ultrahook 【任意】http://【jenkinsホストIP:ポート番号】
```
