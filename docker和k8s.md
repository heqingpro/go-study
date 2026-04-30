### docker 和k8s

#### docker

##### dockerfile 编写命令

- From 基础镜像
- maitainner 维护者信息
- Run 执行命令
- ADD 添加文件 copy文件
- workdir 工作路径
- volume 目录挂载
- expose 开放端口
- Run

#### kubectl 常用命令

- kubectl apply 创建
- kubectl describe pod 【name】查看创建事件信息日志

### 部署pdt k8s

#### 1、kind搭建k8s 集群

- kind create cluster --config kind-example-config.yaml 创建2 worker node 集群

- 配置默认内存
  
  ```
  apiVersion: v1
  kind: LimitRange
  metadata:
    name: mem-limit-range
  spec:
    limits:
    - default:
        memory: 512Mi
      defaultRequest:
        memory: 256Mi
      type: Container
  
   // kubectl apply -f https://k8s.io/examples/admin/resource/memory-defaults.yaml --namespace=default-mem-example
  ```

- 配置k8s集群 docker 仓库secrete
  
  ```
  kubectl create secret docker-registry regcred \
    --docker-server=<你的镜像仓库服务器> \
    --docker-username=<你的用户名> \
    --docker-password=<你的密码> \
    --docker-email=<你的邮箱地址>
  ```

#### 2、docker 私有仓库

- pdt-gateway 修改仓库地址， make publish 上传镜像
