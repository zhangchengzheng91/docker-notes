# 镜像基本操作

|操作|命令|示例|备注|
|:---|:---|:---|:---|
|获取镜像|docker pull **[imagename]**:**[version]**| docker pull ubuntu:18.04 | 如果不指定 TAG，则默认选择 latest。||
|查看镜像列表| docker images |  |  |
|查看镜像列表:name | docker images **[imagename]** | docker images node |  |
|查看镜像列表| docker image ls |  |  |
|添加镜像标签| docker tag **[preImageName]**:**[preImageTag]** **[newImageName]**:**[newImageTag]**| docker tag node:latest mynode:latest| 新镜像的 ID 和 原镜像的 ID 相同|
|查看镜像详情 | docker [image] inspect **[imagename]**:**[version]** | docker inspect node:latest | 详情包括：制作者、适应架构、各层的数字摘要等 |
|获取镜像特定详情| docker [image] inspect -f {{.[attr]}} [imagename]:[tag] | docker inspect -f {{.Architecture}} node:latest | 获取镜像 Architecture 参数|
|查看镜像历史| docker history [imagename]:[tag] | docker history node:latest ||
|搜寻镜像 | docker search [option] keyword | docker search node ||
|使用标签删除镜像 | docker image rmi <imagename>:<tag> | docker rmi node:latest | 当同一个镜像有多个标签时，仅删除该指定标签，不影响镜像文件 |
|使用镜像ID删除镜像 | docker rmi <image id> | docker rm a283f62cb84b | 会同时删除所有相同 ID 的镜像 |
|清理镜像| docker image prune | | 清除遗留的临时镜像文件，以及一些没有被使用的镜像 |
|创建镜像 - 容器| docker [container] commit [OPTIONS] [REPOSITORY[:TAG]] | docker commit -m "Add a new file" -a "Docker Newbee" 9f604c4ea4f5 test:0.1 ||
|创建镜像 - 模版||||
|创建镜像 - Dockerfile |  docker [image] build [REPOSITORY[:TAG]] |||
|存出镜像| docker [image] save | docker save -o ubuntu_18.04.tar ubuntu:18.04 | 导出镜像到指定文件中 |
|载入镜像| docker [image] load | docker load -i ubuntu_18.04.tar | 将导出的 tar 文件再导入本地镜像仓库 |
|上传镜像| docker [image] push | docker |

1. 获取镜像

    ```bash
    docker pull ubuntu:18.04
    ```
   
    如果不指定 TAG，则默认选择 latest。

    一般来说，镜像的 latest 标签意味着该镜像的内容会跟踪最新版本的变更而变化，内容是不稳定的。因此，从稳定性上考虑，不要在生产环境中忽略镜像的标签信息或使用默认的 latest 标记镜像。

    镜像文件一般由若e干层(layer)组成，**docker pull** 命令会获取并输出的各层信息。当不同的镜像包括相同的层时，本地仅存储了层的一份内容，减少了存储空间。

    严格的讲，镜像的仓库名称中还应该添加仓库地址（即 registry，注册服务器）作为前缀，只是默认使用的官方 docker HUB 服务，可以忽略前缀

    ```bash
    docker pull regitry.hub.docker.com/public/ubuntu:18.04
    ```
   
    下载镜像到本地后，即可随时使用该镜像了，例如利用该镜像创建一个容器，在其中运行 bash

    ```bash
    docker run -it ubuntu:18.04 bash
    ```

1. 查看镜像信息

   ```bash
   docker images
   ```

   | 字段 | 含义 | 示例 |
   |:---|:---|:---|
   | REPOSITORY | 仓库来源 | ubuntu 表示 ubuntu 系列的基础镜像 |
   | TAG | 标签，表示版本信息 | 标签只是标记，并不能标示镜像内容 |
   | IMAGE ID | 镜像 ID，镜像唯一标识 | 如果两个镜像 ID 相同，说明他们指向了统一个镜像，只是具有不同的标签名称而已 |
   | CREATED | 镜像的最后更新时间 ||
   | SIZE | 镜像的大小 | 优秀的镜像往往体积都小 |

1. 搜寻镜像

   ```shell
   docker search [option] keyword
   ```
   
   一些特殊的过滤项：
   
   | 示例 | 含义 |
   |:---|:---|
   | docker search --filter=is-official=true nginx | 官方提供的带 nginx 关键字的镜像 |
   | docker search --filter=stars=4 tensorflow | 收藏数超过 4，且关键字包括 tensorflow 的镜像 |
   | docker search --filter=stars=4 --filter=is-official=true node | 收藏数超过 4，官方提供，关键字包含 node 的镜像 |

1. 删除和清理镜像

   ```shell
   docker [image] rmi [imagename]:[tag]
   docker [image] rmi <image id>
   ```
   
   | 选项 | 含义 |
   |:---|:---|
   | -f, --force | 强制删除镜像，即使有容器依赖它 |
   | --no-prune | 不要清理未带标签的父镜像 |

   清理镜像

   ```shell
   docker image prune
   ```
   
1. 创建镜像

   1. 基于已有镜像创建镜像
   
      ```shell
      docker [container] commit [OPTIONS] [REPOSITORY[:TAG]]
      ```
   
      | 选项 | 含义 |
      |:---|:---|
      | -a, --author="" | 作者信息 |
      | -C, --change=[] | 提交时执行 Dockerfile 指令，包括 CMD, ENTRYPOINT, ENV, EXPOSE, LABEL, ONBUILD, USER, VOLUME, WORKDIR |
      | -m, --message="" | 提交信息 |
      | -p, -pause=true | 提交时暂停容器运行 |
   
      ```shell
      # demo
      docker run -it ubuntu:18.04 /bin/bash
      # touch test
      # exit
      docker commit -m "Add a new file" -a "Docker Newbee" 9f604c4ea4f5 test:0.1
      docker run -it test:0.1 /bin/bash
      # ll | grep test
      ```

   1. 基于本地模块导入
   
      用户可以直接从一个操作系统模版文件导入一个镜像
   
      ```shell
      docker [container] import [OPTIONS] file |URL| - [REPOSITORY[:TAG]]
      ```
      
   1. 基于 Dockerfile 创建
   
      ```shell
      docker [image] build [REPOSITORY[:TAG]]
      ```
      
      基于 ebian:stretch-slim 镜像安装 Python3 环境，构建一个新的 pyhton:3 镜像
   
      ```dockerfile
      FROM debian:stretch-slim
      
      LABEL verison="1.0" maintainer="docker user <docker_user@github>"
      
      RUN apt-get update && \
          apt-get install -y python3 && \
          apt-get clean && \
          rm -rf /var/lib/apt/lists/*
      ```
   
1. 存出和载入镜像

   1. 存出镜像
   
      导出本地的 ubuntu:18.04 镜像文件为 ubuntu_18.04.tar
      
      ```shell
      docker save -o ubuntu:18.04 ubuntu_18.04.tar
      ```
      
   1. 载入镜像
   
      将导出的 .tar 镜像再导入本地镜像库
   
      ```shell
      docker load -i ubuntu_18.04.tar
      ```
1. 上传镜像

   ```shell
   docker [image] push NAME[:TAG] | [REPOSITORY_HOST[:REGISTRY_PORT]/]NAME[:TAG]
   
   # demo
   docker tag node zhangchengzheng/test_push_node:latest
   docker push zhangchengzheng/test_push_node:latest
   ```
   
   push 的镜像名必须得是登陆 username 开头
