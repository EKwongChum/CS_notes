# Docker 学习

在Docker官网https://docs.docker.com/get-started/看了一些文档，以下为文档大致摘要。

### 测试Kubernetes

在电脑上安装好 Docker Desktop后，点击设置，选择**启用Kubernetes**

新建一个文件，命名为 pod.yaml ，写入以下内容：

```yaml
 apiVersion: v1
 kind: Pod
 metadata:
   name: demo
 spec:
   containers:
   - name: testpod
     image: alpine:3.5
     command: ["ping", "8.8.8.8"] 
```

在命令行输入以下命令执行这个 yaml文件,创建你的 **pod**

```shell
 kubectl apply -f pod.yaml
```

检查你的 **pod** 是否在运行

```shell
 kubectl get pods
```

关闭运行中的 **pod**

```shell
kubectl delete -f pod.yaml
```

### 启用 Docker Swarm

初始化

```shell
docker swarm init
```

运行一个简单的Docker服务，用基于alpine的文件系统，同样也是 ping 一下 8.8.8.8

```shell
docker service create --name demo alpine:3.5 ping 8.8.8.8
```

确认一下你的服务是否创造了一个正在运行的container：

``` shell
docker service ps demo
```

确认你可以获取到相关的logs信息

```shell
docker service logs demo
```

最后，关闭测试服务

```shell
docker service rm demo
```

## 容器化一个应用

从GitHub的样例来建造一个image吧。

### 准备工作

```shell
git clone -b v1 https://github.com/docker-training/node-bulletin-board
cd node-bulletin-board/bulletin-board-app
```

Dockerfile 描述怎样为一个container编译一个私有文件系统，同时可以包含一些meta数据，这些meta数据描述如何运行一个基于此image的container。例如这个示例的Dockerfile的内容为：

```dockerfile
FROM node:6.11.5

WORKDIR /usr/src/app
COPY package.json .
RUN npm install
COPY . .

CMD [ "npm", "start" ]

```

编写一个 Dockerfile 是容器化一个应用的第一步。你可以一步步地猜测这些 Dockerfile 命令如何构建我们的image。例如上述的文件含义如下：

- 在现有的image （名字为 node:6.11.5）的基础上
- 使用 **WORKDIR** 指定所有的后续操作都在image文件系统的 **/usr/src/app** 下执行，即指定本image（在容器中）的工作目录
- 使用 **COPY** 指令将 package.json 文件从宿主复制到image的当前位置(**.**)，在本例中，就是复制到image的 **/usr/src/app/package.json**
- 使用 **RUN** 指令在你的image文件系统中执行 npm install 命令，在本例，会读取  package.json 文件来安装你的应用的依赖；
- 使用 **COPY** 指令将你的应用的资源代码从宿主复制到image的文件系统

以上命令是用于构造我们的image的文件系统，**CMD** 指令在image中，指定一些meta数据描述如何基于此image运行一个container。在本例中，image容器化的过程是 **npm install**

### 构建和测试你的image

在 node-bulletin-board/bulletin-board-app 目录下，打开命令行，输入以下指令

```shell
docker image build -t bolletinboard:1.0 .
```

构建成功后，使用构建出来的image启动一个container，可以先查看当前宿主的image列表

```shell
docker image ls
```

然后找到刚构建出来的image id，在命令行运行：

```shell
docker container run --publish 8000:8080 --detach --name bb <image id>
```

在这里，我们用了一些flag，例如：

- ---publish 告知docker宿主与container的端口映射关系，例如本例中，宿主的端口 8000 映射到 container的端口 8080 
- --detach  要求Docker把此container在后台运行；
- --name 给运行起来的container起一个名字为 bb；

值得注意的是，我们并没有指定container要运行什么。由于我们在构建image的时候，在Dockerfile中使用了CMD指令，当container运行起来时，Docker自动执行 **npm run** 命令。

可以使用以下指令查看正在运行的container

```shell
docker container ls
```

如果你需要停止容器，可以使用指令 

```shell
docker container rm --force <image id>
```

