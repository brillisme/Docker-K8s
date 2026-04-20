# Harbor 企业级私有仓库搭建与使用教程
## 一、Harbor 简介
Harbor 是 VMware 开源的**企业级 Docker 私有镜像仓库**，用于存储、管理、分发 Docker 镜像，支持镜像复制、权限控制、漏洞扫描、HTTPS 加密等企业级功能，解决了公共镜像仓库不安全、速度慢的问题，是企业内部容器化环境的核心组件。

## 二、环境准备与前置操作
### 1. 卸载冲突服务（删除原有 registry）
```bash
docker rm -f registry
```

### 2. 创建数据目录并部署证书
```bash
mkdir /data
cp -r certs /data
```

## 三、Harbor 安装部署
### 1. 解压安装包
```bash
tar zxf harbor-offline-installer-v2.14.0.tgz
cd harbor/
```

### 2. 复制配置模板
```bash
cp harbor.yml.tmpl harbor.yml
```

### 3. 修改核心配置文件 `harbor.yml`
```bash
vim harbor.yml
```
核心配置内容：
```yaml
hostname: reg.westos.org

http:
  port: 80

https:
  port: 443
  certificate: /data/certs/westos.org.crt
  private_key: /data/certs/westos.org.key

harbor_admin_password: westos
```

### 4. 一键安装 Harbor
```bash
./install.sh --with-trivy
```

<img width="415" height="187" alt="image" src="https://github.com/user-attachments/assets/7bd5d703-64ee-4e1d-a462-7e5ae4ca10d9" />
<img width="416" height="178" alt="image" src="https://github.com/user-attachments/assets/190af05b-f042-45f4-829a-46226fae56c0" />


### 5. 服务状态查看
```bash
# 查看 Harbor 相关镜像
docker compose images

# 查看启动的容器
docker compose ps

# 查看服务运行日志
docker compose logs
```

<img width="415" height="102" alt="image" src="https://github.com/user-attachments/assets/41df8857-3b43-4356-92cd-629799d5ca7d" />
<img width="415" height="111" alt="image" src="https://github.com/user-attachments/assets/a27bf0d8-cfe4-4e86-9dee-c8c361665df9" />



### 6. 访问 Harbor Web 界面
浏览器访问：`https://192.168.201.142`

<img width="415" height="225" alt="image" src="https://github.com/user-attachments/assets/bceda2b4-1bb8-460d-8630-a222ca6293f6" />
<img width="415" height="236" alt="image" src="https://github.com/user-attachments/assets/f4119382-f0fc-4b5d-9a65-fcd759000c4d" />


## 四、Harbor 基础使用
### 1. 配置域名解析
```bash
echo "192.168.201.142 reg.westos.org" | sudo tee -a /etc/hosts
```

### 2. 登录 Harbor 私有仓库
```bash
docker login reg.westos.org
```
输入用户名：`admin`，密码：`westos`，提示 `Login Succeeded` 即为登录成功。

<img width="415" height="82" alt="image" src="https://github.com/user-attachments/assets/7eb4bf01-133f-4938-a532-a02f4b05c461" />


### 3. 为镜像打标签（适配 Harbor 仓库格式）
```bash
docker tag busybox:latest reg.westos.org/library/busybox:latest
docker tag nginx:latest reg.westos.org/library/nginx:latest
```

### 4. 上传镜像到 Harbor
```bash
docker push reg.westos.org/library/nginx:latest
docker push reg.westos.org/library/busybox:latest
```

<img width="416" height="84" alt="image" src="https://github.com/user-attachments/assets/8994d0c9-62d4-4f6a-93a8-90d2c40c66b4" />
<img width="416" height="37" alt="image" src="https://github.com/user-attachments/assets/e173a7bf-c1ae-47f7-8f36-bda3b9c5ed02" />
<img width="416" height="108" alt="image" src="https://github.com/user-attachments/assets/ad38c48f-515b-4201-8599-cf50ddb886c7" />





## 五、创建私有仓库
在 Harbor Web 界面中：
1. 左侧点击**项目** → 右上角**新建项目**

<img width="415" height="354" alt="image" src="https://github.com/user-attachments/assets/44aae058-30a4-408c-9d79-dd126444b86e" />

2. 填写项目名称，**取消勾选「公开」** 即为私有仓库

<img width="416" height="191" alt="image" src="https://github.com/user-attachments/assets/acd9f45f-0787-4ccc-a08c-967f8fc072ed" />

3. 配置项目成员与权限，完成私有仓库创建

---
