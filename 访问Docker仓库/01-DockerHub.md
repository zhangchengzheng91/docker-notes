# Docker Hub 公有镜像市场

根据是否为官方提供，可将这些资源分为两类:

一种是类似于 centos 这样的基础镜像，也称为根镜像。这些镜像是有 Docker 公司创建、验证、支持、提供，这样的镜像往往使用单个单词作为名字；

另一种类型的镜像，比如 ansible/centos7-ansible 镜像，是由 Docker 用户 ansible 创建并维护的，带有用户名称为前缀，表明是某个用户下的某仓库。可以通过用户名前缀"""

1. 登录

   ```shell
   docker login
   ```
   
1. 基本操作

   ```shell
   # 查找镜像
   docker search <keyword>
   # 下载镜像
   docker [image] pull <imageName>
   # 推送镜像到 Docker Hub
   docker push
   ```
   
1. 自动创建

   自动创建（Automated Builds） 是 Docker Hub 提供的自动化服务，这一功能可以自动跟随项目代码的变更而重新构建镜像。

   例如，用户构建了某应用镜像，如果应用发布新版本，用户需要手动更新镜像。而自动创建则用于用户同多 Docker Hub 指定跟踪一个目标网站（目前支持 GitHub 和 BitBucket）上的项目，一旦项目发生新的提交，则自动执行创建。

   要配置自动创建，包括如下的步骤：

   1. 创建并登陆 Docker Hub，以及目标网站，如： GitHub
   1. 将目标网站中允许 Docker Hub 访问服务
   1. 在 Docker Hub 中配置一个"自动创建"类型项目
   1. 选取一个目标网站中的项目（需要包含 Dockerfile）和分支
   1. 指定 Dockerfile 的位置，并提交创建
