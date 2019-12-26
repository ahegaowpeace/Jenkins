FROM centos:7

# Jenkins用諸パッケージ
RUN yum -y update
RUN yum -y install wget
RUN yum -y install java
RUN yum -y install git
RUN yum -y install initscripts

# Jenkins yumリポジトリ登録
RUN wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo
RUN rpm --import http://jenkins-ci.org/redhat/jenkins-ci.org.key

# Jenkinsインストール
RUN yum -y install jenkins


# 秘密鍵用ファイル作成
RUN mkdir /var/lib/jenkins/.ssh
RUN chmod 700 /var/lib/jenkins/.ssh
RUN touch /var/lib/jenkins/.ssh/private.pem
RUN chmod 600 /var/lib/jenkins/.ssh/private.pem
RUN chown -R jenkins:jenkins /var/lib/jenkins/.ssh
