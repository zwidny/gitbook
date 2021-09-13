FROM jenkins/jenkins:lts-jdk11
USER root
RUN sed -i 's/deb.debian.org/mirrors.tuna.tsinghua.edu.cn/g' /etc/apt/sources.list
RUN sed -i 's/security.debian.org/mirrors.tuna.tsinghua.edu.cn/g' /etc/apt/sources.list
RUN apt-get update && apt-get install -y apt-transport-https \
ca-certificates curl gnupg2 \
software-properties-common
RUN curl -fsSL https://download.docker.com/linux/debian/gpg | apt-key add -
RUN apt-key fingerprint 0EBFCD88
RUN add-apt-repository \
"deb [arch=amd64] https://download.docker.com/linux/debian \
$(lsb_release -cs) stable"
RUN apt-get update && apt-get install -y docker-ce-cli
USER jenkins
RUN jenkins-plugin-cli --plugins "blueocean:1.24.7 docker-workflow:1.26" \
--jenkins-update-center 'https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json' \
--jenkins-experimental-update-center 'https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/experimental/update-center.json' \
--jenkins-plugin-info https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/current/plugin-versions.json --verbose