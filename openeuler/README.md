# 基于openeuler构建Apache APISIX网关相关镜像
Apache APISIX 是一个动态、实时、高性能的云原生 API 网关。包含四个部分：
1. APISIX - 核心，提供路由匹配、负载均衡、服务发现、API 管理等重要功能
2. ETCD - 作为配置中心进行保存和同步配置
3. Dashboard - 管理界面
4. Ingress Controller - Kubernetes ingress controller实现，读取Kubernetes原生Ingress或Gateway API资源，并转换为APISIX配置，并提供了APISIX自定义资源对象

## APISIX
1. 首先构建APISIX的rpm包，参考https://github.com/opensourceways/apisix-build-tools/blob/build-base-openeuler/README.md
2. 复制包到openueler/apisix目录
   ```
   cd openeuler/apisix
   cp /path/to/apisix-3.9.1-0.el7.x86_64.rpm .
   ```
3. 创建镜像
   ```
   docker build -t apache/apisix:3.9.1-rpm-alpha -f Dockerfile .
   ```
## ETCD
1. 复制指定分支代码到本地
   ```
   git clone -b v3.5.14 https://github.com/opensourceways/etcd.git
   ```
2. 通过docker构建服务
   ```
   // a.运行一个golang:1.7.5的服务，挂载本地etcd源码/path/to/etcd到容器/opt/etcd目录，启动run，并进入容器：-it bash
   docker run -v /path/to/etcd/:/opt/etcd -it --rm golang:1.22.4 bash
   // b. 进入容器源码挂载目录
   cd /opt/etcd
   // c. 编译
   make
   ```
   成功后可见源码目录下新增的bin目录有三个可执行文件etcd  etcdctl  etcdutl
   ```
   cd etcd/bin
   ls
   etcd  etcdctl  etcdutl
   ```
3. 复制上述三个执行文件到构建目录
   ```
   cp -ri /etcd/bin/* openeuler/etcd
   ```
4. 创建镜像
   ```
   docker build -t etcd-io/etcd:3.5.14-alpha -f ./Dockerfile .
   ```
## Dashboard
1. 创建镜像
   ```
   cd openeuler/dashboard

   docker build -t apache/apisix-dashboard:3.0.1 --build-arg APISIX_DASHBOARD_VERSION=v3.0.1 -f ./Dockerfile .
   ```
## Ingress Controller
1. 复制指定分支代码到本地
   ```
   git clone -b v1.8.2 https://github.com/opensourceways/apisix-ingress-controller.git
   cd apisix-ingress-controller
   ```
2. 修改Dockefile基础镜像
   ```
   vim Dockerfile
   ```
   修改最后一层构建镜像

   before
   ```
   FROM gcr.io/distroless/static-debian12:${BASE_IMAGE_TAG}
   ```
   after
   ```
   FROM openeuler/openeuler:22.03
   ```
3. 创建镜像
   ```
   DOCKER_BUILDKIT=1 docker build -t apache/apisix-ingress-controller:1.8.2  .
   ```
