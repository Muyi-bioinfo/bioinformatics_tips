
---

## Docker 常用命令与参数解释

| 分类        | 命令                                               | 参数/用法                                    | 说明              |
| --------- | ------------------------------------------------ | ---------------------------------------- | --------------- |
| **镜像操作**  | `docker pull <image>`                            | `<image>` = 镜像名:版本                       | 拉取镜像            |
|           | `docker pull --platform=xxxxxx <image>`          | `--platform=linux/amd64` 或 `linux/arm64` | 指定平台拉取          |
|           | `docker images`                                  | 无                                        | 查看本地镜像          |
|           | `docker rmi <image_id>`                          | `<image_id>` = 镜像 ID                     | 删除镜像            |
| **容器运行**  | `docker run <image>`                             | 无                                        | 前台运行容器          |
|           | `docker run -p 80:80 <image>`                    | `-p 主机端口:容器端口`                           | 端口映射            |
|           | `docker run -it <image>`                         | `-i` 保持输入, `-t` 分配终端                     | 交互式运行           |
|           | `docker run -it --rm <image>`                    | `--rm` 自动删除容器                            | 临时运行            |
|           | `docker run --name my_container <image>`         | `--name` 指定容器名                           | 自定义名字           |
|           | `docker run -it -v /host:/container <image>`     | `-v 主机目录:容器目录`                           | 挂载目录            |
|           | `docker run -d <image>`                          | `-d` 后台运行                                | 后台模式            |
|           | `docker run -d --restart unless-stopped <image>` | `--restart` 自动重启策略                       | 自动重启            |
|           | `docker run -d -e KEY=VALUE <image>`             | `-e` 设置环境变量                              | 设置配置，如数据库账号密码   |
| **容器管理**  | `docker ps`                                      | 无                                        | 查看运行中容器         |
|           | `docker ps -a`                                   | `-a` 显示所有容器                              | 包括停止的           |
|           | `docker create <image>`                          | 无                                        | 创建但不启动          |
|           | `docker stop <id>`                               | `<id>` = 容器 ID/名                         | 停止容器            |
|           | `docker start <id>`                              | 无                                        | 启动容器            |
|           | `docker restart <id>`                            | 无                                        | 重启容器            |
|           | `docker rm -f <id>`                              | `-f` 强制删除                                | 删除容器            |
|           | `docker inspect <id>`                            | 无                                        | 查看容器详细信息 (JSON) |
| **进入容器**  | `docker exec -it <id> bash`                      | `-it` 交互模式                               | 打开 bash 终端      |
|           | `docker attach <id>`                             | 无                                        | 进入主进程，退出会关掉容器   |
| **日志与监控** | `docker logs <id>`                               | 无                                        | 查看日志            |
|           | `docker logs -f <id>`                            | `-f` 实时跟随                                | 持续输出日志          |
|           | `docker stats`                                   | 无                                        | 监控 CPU/内存/网络等   |
| **构建镜像**  | `docker build -t name:tag .`                     | `-t` 指定名称:版本                             | 从 Dockerfile 构建 |
|           | `docker tag <image_id> <repo:tag>`               | `<repo:tag>` 新标签                         | 给镜像打标签          |
| **数据卷**   | `docker volume ls`                               | 无                                        | 列出卷             |
|           | `docker volume create <name>`                    | `<name>` 卷名                              | 创建卷             |
|           | `docker volume inspect <name>`                   | 无                                        | 查看卷信息           |
|           | `docker volume rm <name>`                        | 无                                        | 删除卷             |
| **系统清理**  | `docker system df`                               | 无                                        | 查看磁盘占用          |
|           | `docker system prune`                            | 无                                        | 清理未使用的资源        |
|           | `docker image prune`                             | 无                                        | 清理未使用的镜像        |

---
