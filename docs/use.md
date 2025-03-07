
### 修改端口(自定义配置)

docker\.env

```bash

# HTTP port
NGINX_PORT=8083
# SSL settings are only applied when HTTPS_ENABLED is true
NGINX_SSL_PORT=8443

EXPOSE_NGINX_PORT=8083
EXPOSE_NGINX_SSL_PORT=8443


# 编辑 .env 文件中的环境变量值。然后重新启动 Dify：

docker compose down
docker compose up -d


```

### 启动 Dify

```bash

cd dify/docker

cp .env.example .env

docker compose up -d
# (or 老版本 docker-compose up -d)

```

### 更新

```bash

cd dify/docker
docker compose down
git pull origin main
docker compose pull
docker compose up -d

```

### 访问 Dify

你可以先前往管理员初始化页面设置设置管理员账户：

```bash
# 本地环境
http://localhost/install

# 服务器环境
http://your_server_ip/install
```

Dify 主页面：

```bash
# 本地环境
http://localhost

# 服务器环境
http://your_server_ip

```

