# Jenkins 篇 - 配置Jenkins Slave
本文主要介绍如何使用jenkins/ssh-agent images配置jenkins slave. 另外此文基于[上篇](jenkins.md)文章， 如果有任何疑问，请先阅读之

## Step 1 安装Jenkins [Docker Plugin](https://plugins.jenkins.io/docker-plugin/#releases)

   一旦安装成功，便可以在"系统管理">"节点管理">"Configure Clouds"下配置Docker Cloud了  

## Step 2 配置Docker Cloud
   
   1. 添加Docker Cloud
   
      ![image](../../images/screenshot-127.0.0.1_8080-2021.09.06-15_30_17.png)
      
      一旦添加后， 会出现如上图所示的Docker 配置
   2. 配置Docker Cloud
   
      点击"Docker Cloud details..."会出现如下图所示的配置. 如果您是按照[上文](jenkins.md)来的， 按图类似配置即可
   
      1. 配置Docker Cloud

         ![image](../../images/docker_cloud_config.png)
   
      2. 配置Server credentials

         ![image](../../images/x_509.png)
   
   3. 配置Docker Agent Template

      图片中可能用的文字[jenkins/ssh-agent:jdk11](https://hub.docker.com/r/jenkins/ssh-agent)
    
      ![image](../../images/docker_template_conf.png)
