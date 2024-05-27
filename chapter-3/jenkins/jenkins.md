# Jenkins 篇 - 安装Jenkins
本文主要介绍如何使用jenkins docker images部署Jenkins. 另外加入了国内优化, 包括更新了docker registry以及Jenkins update center.

1. 首先定义要用到的变量
   ```shell
   export JENKINS_DOCKER_CERTS=~/DockerVolumes/docker-dind/
   export JENKINS_HOME=~/DockerVolumes/jenkins/
   ```
2. Create a bridge network in Docker using the following docker network create command:
   ```shell
   docker network create jenkins
   ```
3. In order to execute Docker commands inside Jenkins nodes, download and run the docker:dind Docker image using the following docker run command:
   ```shell
   docker run --name jenkins-docker --detach \
   --privileged --network jenkins --network-alias docker \
   --env DOCKER_TLS_CERTDIR=/certs \
   --volume $JENKINS_DOCKER_CERTS:/certs/client \
   --volume $JENKINS_HOME:/var/jenkins_home \
   --restart=always \
   --publish 2376:2376 docker:dind --storage-driver overlay2 --registry-mirror 'https://ktuwsbx0.mirror.aliyuncs.com'
   ```
4. Customise official Jenkins Docker image, by executing below two steps:  
   方式一: 使用我已经build好的image
      ```shell
      docker pull swr.cn-north-4.myhuaweicloud.com/cn/jenkins:lts-jdk11-cn
      ```
   方式二: 自建Dockerfile
      1. Create Dockerfile with the following content:
         ```shell
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
         ```
      2. Build a new docker image from this Dockerfile and assign the image a meaningful name, e.g. "jenkins-zh-cn" 
         ```shell
         docker build -t jenkins:lts-jdk11-cn .
         ```
5. Run as container
   ```shell
   docker run --name jenkins-lts --detach \
   --network jenkins \
   --restart=always \
   --env DOCKER_HOST=tcp://docker:2376 \
   --env DOCKER_CERT_PATH=/certs/client \
   --env DOCKER_TLS_VERIFY=1 \
   --publish 8080:8080 --publish 50000:50000 \
   --volume $JENKINS_HOME:/var/jenkins_home \
   --volume $JENKINS_DOCKER_CERTS:/certs/client:ro \
   jenkins:lts-jdk11-cn
   ```

