# 基础知识

如果是通过包管理器安装的nginx，则**主配置文件**位于 `/etc/nginx/nginx.conf`，**网站配置目录**可以选择 `/etc/nginx/conf.d/` 或 `/etc/nginx/sites-available/` 和 `/etc/nginx/sites-enabled/`

`conf.d/` 和 `sites-enabled/` 都可以用来存放 Nginx 的网站或应用配置，它们代表了两种不同的组织和管理配置文件的**风格**。

## `conf.d/` 风格

**特点：** 简单直接，将所有的独立网站或应用配置文件直接放在 `conf.d/` 目录下（通常以 `.conf` 结尾）。

**如何工作：** Nginx 的主配置文件 `nginx.conf` 中通常会有一行：
```nginx
include /etc/nginx/conf.d/*.conf;
```

**优点：**
- **简单明了：** 配置文件的位置一目了然。
- **易于管理：** 添加、删除或修改配置文件都非常直接。

**缺点：**
- **启用/禁用不便：** 如果你想临时禁用一个网站，你需要手动删除或移动它的配置文件，或者注释掉 `include` 指令中的相应行。

**适用场景：** 适用于大多数情况，特别是当你不需要频繁启用/禁用网站时。在 CentOS/RHEL 系统上更常见。

## `sites-available/` 和 `sites-enabled/` 风格

**特点：** 这种风格在 Debian/Ubuntu 系统上非常流行，它通过软链接来管理网站的启用和禁用。

**如何工作：**
1. **`sites-available/`：** 这个目录存放所有可用的网站配置文件。即使文件在这里，Nginx 也不会加载它们。
2. **`sites-enabled/`：** 这个目录存放当前正在运行的网站的配置文件。这些文件实际上是 `sites-available/` 目录中对应文件的**软链接**。
3. Nginx 的主配置文件 `nginx.conf` 中通常会有一行：

```nginx
include /etc/nginx/sites-enabled/*;
```

**启用网站：** 创建一个从 `sites-available/` 到 `sites-enabled/` 的软链接。 

```bash
sudo ln -s /etc/nginx/sites-available/your_site.conf /etc/nginx/sites-enabled/your_site.conf
```

**禁用网站：** 删除 `sites-enabled/` 目录中的软链接。

```bash
sudo rm /etc/nginx/sites-enabled/your_site.conf
```

**优点：**
- **方便启用/禁用：** 无需修改或移动实际的配置文件，只需创建或删除软链接即可。
- **清晰的启用状态：** `sites-enabled/` 目录一目了然地显示了哪些网站正在运行。

**缺点：**
- **多一步操作：** 每次添加新网站都需要创建软链接。
- **软链接管理：** 需要了解软链接的概念和操作。

**适用场景：** 适用于需要频繁启用/禁用网站、或者管理大量虚拟主机的环境。在 Debian/Ubuntu 系统上更常见。

## 配置文件

一个典型的Nginx配置文件结构如下：

```nginx
# 全局块 (Global Block)
# 影响Nginx服务器整体的配置，例如工作进程数、错误日志路径等。
user  nginx; # Nginx工作进程的用户
worker_processes  auto; # 工作进程数，auto表示根据CPU核心数自动设置

error_log  /var/log/nginx/error.log warn; # 错误日志路径和级别
pid        /var/run/nginx.pid; # Nginx主进程的PID文件

events {
    # 事件块 (Events Block)
    # 配置Nginx网络连接的特性，例如最大连接数、I/O模型等。
    worker_connections  1024; # 每个工作进程的最大并发连接数
    # use epoll; # Linux系统下推荐使用epoll I/O模型
}

http {
    # HTTP块 (HTTP Block)
    # 配置HTTP服务器相关的特性，例如MIME类型、日志格式、虚拟主机等。
    include       mime.types; # 引入MIME类型映射文件
    default_type  application/octet-stream; # 默认MIME类型

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"'; # 日志格式

    access_log  /var/log/nginx/access.log  main; # 访问日志路径和格式

    sendfile        on; # 开启高效文件传输模式
    #tcp_nopush     on; # 配合sendfile使用，提高传输效率

    keepalive_timeout  65; # 保持连接的超时时间

    # gzip  on; # 开启Gzip压缩，提高传输效率

    # server 块 (Server Block)
    # 定义一个虚拟主机，可以监听特定的端口和域名。
    server {
        listen       80; # 监听端口
        server_name  example.com www.example.com; # 域名

        # location 块 (Location Block)
        # 定义URL路径匹配规则，并进行相应的处理。
        location / {
            root   /usr/share/nginx/html; # 网站根目录
            index  index.html index.htm; # 默认首页文件
        }

        location /api/ {
            # 反向代理到后端API服务器
            proxy_pass http://backend_servers; # 代理目标
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            # ... 其他代理配置
        }

        # 错误页配置
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   /usr/share/nginx/html;
        }
    }

    # upstream 块 (Upstream Block)
    # 定义一组后端服务器，用于负载均衡。
    upstream backend_servers {
        server 192.168.1.100:8080;
        server 192.168.1.101:8080;
        # 负载均衡策略，默认是轮询
        # ip_hash;
        # least_conn;
    }
}
```

**主要配置块解释：**

- **`main` (全局块)：** 配置Nginx服务器的全局参数，如用户、工作进程数、错误日志等。
- **`events` 块：** 配置Nginx处理网络连接的方式，如每个工作进程的最大连接数、使用的I/O模型等。
- **`http` 块：** 配置HTTP服务器的通用参数，如MIME类型、日志格式、Gzip压缩等。所有的`server`块都必须放在`http`块内。
- **`server` 块：** 定义一个虚拟主机。一个Nginx实例可以包含多个`server`块，每个`server`块可以监听不同的端口、处理不同的域名。
    - `listen`：指定监听的端口。
    - `server_name`：指定该虚拟主机处理的域名。
- **`location` 块：** 定义URL匹配规则。当客户端请求的URL与某个`location`块匹配时，Nginx会按照该块内的指令进行处理。
    - `root`：指定该`location`对应的文件系统路径。
    - `index`：指定默认的首页文件。
    - `proxy_pass`：用于反向代理，将请求转发到指定的后端服务器。
- **`upstream` 块：** 定义一组后端服务器，通常用于负载均衡。可以在`location`块中使用`proxy_pass`指令引用`upstream`块中定义的服务器组。

# 常用命令

- **启动Nginx：** `sudo systemctl start nginx`
- **停止Nginx：** `sudo systemctl stop nginx`
- **重新加载配置文件（平滑重启）：** `sudo systemctl reload nginx`
- **测试配置文件语法：** `sudo nginx -t`
- **进程管理：** `sudo systemctl status nginx`
- **查看Nginx进程：** `ps -ef | grep nginx`

# 反向代理

## 基本配置

假设你有一个后端服务器运行在 `http://127.0.0.1:8080`，我希望通过Nginx的80端口访问它

```nginx
http {
    # ... 其他HTTP配置 ...

    server {
        listen 80; # Nginx监听的端口
        server_name your_domain.com; # 域名，或者可以是IP地址

        location / {
            # proxy_pass 指令是反向代理的核心
            # 它将所有匹配此location的请求转发到指定的后端URL
            proxy_pass http://127.0.0.1:8080;

            # 以下是常用的代理头设置
            # 它们确保后端服务器能正确获取客户端的真实IP、Host等信息
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
}
```
**解释：**
- **`listen 80;`**: Nginx监听80端口，等待HTTP请求。
- **`server_name your_domain.com;`**: 当请求的`Host`头是`your_domain.com`时，这个`server`块会被匹配。
- **`location / { ... }`**: 这是一个通用的`location`块，匹配所有以`/`开头的URL路径。这意味着所有进入`your_domain.com`的请求都会被这个`location`块处理。
- **`proxy_pass http://127.0.0.1:8080;`**: 这是反向代理的核心指令。它告诉Nginx将所有匹配此`location`的请求转发到`http://127.0.0.1:8080`这个后端地址。
- **`proxy_set_header ...;`**: 这些指令用于修改或添加请求头，以便后端服务器能够获取到客户端的真实信息：
    - **`Host $host;`**: 将客户端请求的`Host`头原样转发给后端。如果后端应用程序依赖`Host`头来识别虚拟主机，这个很重要。
    - **`X-Real-IP $remote_addr;`**: 将客户端的真实IP地址传递给后端。
    - **`X-Forwarded-For $proxy_add_x_forwarded_for;`**: 当请求经过多个代理时，这个头会记录所有代理的IP地址。`$proxy_add_x_forwarded_for`会自动添加Nginx的IP和客户端的真实IP。
    - **`X-Forwarded-Proto $scheme;`**: 传递客户端请求的协议（HTTP或HTTPS）。

##  反向代理到不同的路径

可以根据URL路径将请求代理到不同的后端服务

```nginx
http {
    server {
        listen 80;
        server_name your_domain.com;

        # 代理到后端API服务
        location /api/ {
            proxy_pass http://127.0.0.1:3000; # 假设API服务在3000端口
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }

        # 代理到后端管理界面服务
        location /admin/ {
            proxy_pass http://127.0.0.1:4000; # 假设管理服务在4000端口
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }

        # 默认处理其他所有请求（例如静态文件或主应用）
        location / {
            proxy_pass http://127.0.0.1:8080; # 假设主应用在8080端口
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
    }
}
```

**注意 `proxy_pass` URL末尾的斜杠：**

- **`proxy_pass http://backend_server/;` (带斜杠):** Nginx会将`location`匹配到的部分**去除**，然后将剩余的URL路径附加到`proxy_pass`指定的URL后面。
    - 例如：`location /api/` 和 `proxy_pass http://127.0.0.1:3000/;`
    - 请求 `http://your_domain.com/api/users` 会被代理到 `http://127.0.0.1:3000/users`。（**`api`没了**）
- **`proxy_pass http://backend_server;` (不带斜杠):** Nginx会将`location`匹配到的部分**保留**，然后将整个匹配到的URL路径附加到`proxy_pass`指定的URL后面。
    - 例如：`location /api/` 和 `proxy_pass http://127.0.0.1:3000;`
    - 请求 `http://your_domain.com/api/users` 会被代理到 `http://127.0.0.1:3000/api/users`。（**`api`还在**）

选择哪种取决于后端服务期望的URL结构

## 负载均衡

可以定义一个`upstream`块来包含多个后端服务器

```nginx
http {
    # 定义后端服务器组
    upstream my_backend_servers {
        server 192.168.1.100:8080; # 后端服务器1
        server 192.168.1.101:8080; # 后端服务器2
        server 192.168.1.102:8080; # 后端服务器3

        # 负载均衡策略 (可选，默认是轮询)
        # 轮询 (Round Robin): 默认，按顺序分发请求。
        # least_conn; # 最少连接数：将请求发送到当前连接数最少的服务器。
        # ip_hash;    # IP哈希：根据客户端IP的哈希值分发请求，确保同一客户端的请求总是发送到同一台服务器。
        # fair;       # (需要第三方模块) 根据后端响应时间进行分发。
        # hash $request_uri; # 根据URI哈希
        # sticky;     # (需要第三方模块) 会话保持
    }

    server {
        listen 80;
        server_name your_domain.com;

        location / {
            # proxy_pass 指向 upstream 块的名称
            proxy_pass http://my_backend_servers;

            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
}
```

**解释：**

- **`upstream my_backend_servers { ... }`**: 定义了一个名为`my_backend_servers`的后端服务器组。
- **`server 192.168.1.100:8080;`**: 列出组内的每个后端服务器及其端口。
- **负载均衡策略：**
    - **轮询 (Round Robin):** 这是默认策略。请求会依次分发给组内的每个服务器。
    - **`least_conn;`**: 将新请求发送到当前活动连接数最少的服务器。适用于连接时间差异较大的情况。
    - **`ip_hash;`**: 根据客户端IP地址的哈希值决定将请求发送到哪个服务器。这可以实现简单的会话保持，确保同一客户端的请求始终由同一台后端服务器处理。
- **`proxy_pass http://my_backend_servers;`**: 在`location`块中，`proxy_pass`指向`upstream`块的名称，Nginx就会自动进行负载均衡。

# 签署SSL证书

为网站自动签署 SSL 证书，最流行和推荐的方法是使用 **Let's Encrypt** 及其客户端工具 **Certbot**。Let's Encrypt 提供免费、自动化、开放的证书，而 Certbot 则是获取和续订这些证书的官方推荐工具。

开始之前，需确保：
1. **域名已正确解析到你的服务器 IP 地址。**
2. **Nginx 服务器已正确安装并正在运行，并且监听 80 端口。** Certbot 默认使用 HTTP-01 挑战来验证域名，这需要 Nginx 能够响应 80 端口上的请求。

## 安装 Certbot

- **Ubuntu/Debian：**

```bash
sudo apt update
sudo apt install certbot python3-certbot-nginx
```

- **CentOS/RHEL：**

```bash
sudo yum install epel-release
sudo yum install certbot python3-certbot-nginx
```

## 自动扫描配置

Certbot 的 `nginx` 插件可以自动检测 Nginx 配置，并安装证书。

```bash
sudo certbot --nginx
```

运行此命令后，Certbot 会：

1. **扫描 Nginx 配置：** 它会查找 `server_name` 指令，列出它找到的所有域名。
2. **提示选择域名：** 输入你想要为其启用 HTTPS 的域名对应的数字（例如 `1 2`，或直接回车选择全部）。

```txt
Which names would you like to activate HTTPS for?
-------------------------------------------------------------------------------
1: example.com
2: www.example.com
-------------------------------------------------------------------------------
Select the appropriate numbers separated by commas and/or spaces, or leave input
blank to select all options shown (Enter 'c' to cancel):
```

3. **执行域名验证：** Certbot 会在 Nginx 配置中临时添加一个 `location` 块，用于响应 Let's Encrypt 验证服务器的请求（HTTP-01 挑战）。
4. **获取证书：** 如果验证成功，Let's Encrypt 会颁发证书。
5. **修改 Nginx 配置：** Certbot 会自动修改你的 Nginx 配置文件，添加 `listen 443 ssl;`、`ssl_certificate`、`ssl_certificate_key` 等指令，并配置推荐的 SSL/TLS 设置。它还会询问你是否将 HTTP 请求重定向到 HTTPS。

```txt
Please choose whether or not to redirect HTTP traffic to HTTPS, removing HTTP access.
-------------------------------------------------------------------------------
1: No redirect - Make no changes to the webserver configuration.
2: Redirect - Make all requests redirect to secure HTTPS access. Choose this for
new sites, or if you're confident your site works on HTTPS. You can undo this
change by editing your web server's configuration.
-------------------------------------------------------------------------------
Select the appropriate number [1-2] then [enter] (press 'c' to cancel):
```

6. **重新加载 Nginx：** Certbot 会自动重新加载 Nginx 配置，使证书生效。

## 指定域名

可以直接在 `certbot --nginx` 命令后面加上 `-d` 参数来指定一个或多个域名

```bash
sudo certbot --nginx -d example.com
```

```bash
sudo certbot --nginx -d site1.com -d www.site1.com -d blog.site2.net
```

# location 匹配优先级

`location` 块是 Nginx 配置中非常重要的一部分，它决定了如何根据客户端请求的 URI（统一资源标识符）来处理请求。理解 `location` 块的匹配优先级对于正确配置 Nginx 的路由行为至关重要。

Nginx 会根据一套规则来选择最匹配请求 URI 的 `location` 块。当有多个 `location` 块可能匹配同一个 URI 时，优先级就起作用了。

`location` 块的语法通常是：

```nginx
location [修饰符] 路径 {
    # 配置指令
}
```

这里的 **修饰符** 决定了匹配的类型和优先级。

## `location` 匹配修饰符及其优先级

Nginx 按照以下顺序评估 `location` 块：

1. **`=` (精确匹配)**
    - **语法：** `location = /path/to/file`
    - **优先级：最高。**
    - **行为：** 要求请求 URI **完全等于** `路径`。如果找到精确匹配，Nginx 会立即停止搜索并处理该 `location` 块。
    - **适用场景：** 用于处理特定文件或路径，例如 `/favicon.ico` 或 `/robots.txt`，可以避免不必要的正则表达式匹配，提高效率。
2. **`^~` (前缀匹配，优先于正则表达式)**
    - **语法：** `location ^~ /path/to/prefix`
    - **优先级：次高。**
    - **行为：** 如果请求 URI 以 `路径` 开头，并且这是一个最长的前缀匹配，Nginx 会选择这个 `location` 块，并 **停止搜索** 其他（包括正则表达式）`location` 块。
    - **适用场景：** 用于处理特定目录下的静态文件，或者当你知道某个路径下的请求不应该被正则表达式处理时。
3. **`~` 或 `~*` (正则表达式匹配)**
    - **语法：**
        - `location ~ pattern` (区分大小写)
        - `location ~* pattern` (不区分大小写)
    - **优先级：中等。**
    - **行为：** Nginx 会检查所有正则表达式 `location` 块，并选择第一个匹配的。如果存在多个匹配的正则表达式 `location` 块，Nginx 会选择在配置文件中 **定义顺序靠前** 的那个。
    - **适用场景：** 用于更复杂的 URL 匹配，例如根据文件扩展名、动态参数等进行路由。
4. **无修饰符 (前缀匹配)**
    - **语法：** `location /path/to/prefix`
    - **优先级：较低。**
    - **行为：** 如果请求 URI 以 `路径` 开头，Nginx 会选择最长的匹配前缀。
    - **适用场景：** 最常见的匹配方式，用于定义目录级别的配置，例如 `/images/` 或 `/css/`。
5. **`/` (通用匹配)**
    - **语法：** `location /`
    - **优先级：最低。**
    - **行为：** 匹配所有请求。如果前面的所有 `location` 块都没有匹配成功，Nginx 会使用这个块。
    - **适用场景：** 通常作为默认的请求处理器，用于处理未被其他 `location` 块捕获的请求，例如提供默认的静态文件服务或反向代理到主应用。

**总结一下** Nginx 匹配 `location` 块的顺序如下：

1. **精确匹配 (`=`)：** 首先尝试所有 `=` 精确匹配。如果找到一个完全匹配的，立即停止搜索并使用该 `location`。
2. **前缀匹配 (`^~`)：** 接下来，Nginx 会检查所有 `^~` 前缀匹配。它会选择最长的前缀匹配。如果找到一个最长的前缀匹配，并且它带有 `^~` 修饰符，则停止搜索并使用该 `location`。
3. **正则表达式匹配 (`~` 或 `~*`)：** 如果没有找到精确匹配或带有 `^~` 的最长前缀匹配，Nginx 会按它们在配置文件中定义的顺序，尝试匹配所有正则表达式 `location` 块。第一个匹配的正则表达式 `location` 块将被使用。
4. **普通前缀匹配 (无修饰符)：** 如果以上所有都没有匹配成功，Nginx 会选择所有普通前缀匹配（无修饰符）中最长的那个。
5. **通用匹配 (`/`)：** 如果所有其他 `location` 块都未能匹配，最终会使用 `location /` 块。

## 示例

```nginx
server {
    listen 80;
    server_name example.com;

    # 1. 精确匹配：最高优先级
    # 只有当请求是 /favicon.ico 时才匹配
    location = /favicon.ico {
        root /var/www/html/static;
        log_not_found off; # 不记录找不到文件的错误
        access_log off;
    }

    # 2. 前缀匹配，优先于正则表达式
    # 匹配所有以 /static/ 开头的请求
    # 例如 /static/css/style.css 会匹配到这里，并且不会再检查正则表达式
    location ^~ /static/ {
        root /var/www/html; # 实际路径是 /var/www/html/static/
        expires 30d; # 缓存30天
    }

    # 3. 正则表达式匹配 (区分大小写)
    # 匹配所有以 .php 结尾的请求
    # 如果请求是 /index.php，会匹配到这里
    # 如果有多个正则表达式匹配，按配置文件顺序，第一个匹配的会被使用
    location ~ \.php$ {
        proxy_pass http://127.0.0.1:9000; # 假设是PHP-FPM
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }

    # 4. 正则表达式匹配 (不区分大小写)
    # 匹配所有以 .jpg 或 .png 结尾的图片请求
    location ~* \.(jpg|png)$ {
        root /var/www/html/images;
        expires 7d;
    }

    # 5. 普通前缀匹配 (最长匹配)
    # 匹配所有以 /api/ 开头的请求
    # 例如 /api/users 会匹配到这里
    location /api/ {
        proxy_pass http://backend_api_server;
    }

    # 6. 通用匹配：最低优先级，捕获所有未被其他 location 匹配的请求
    # 例如 /index.html 或 /aboutus
    location / {
        root /var/www/html/app;
        index index.html index.htm;
        try_files $uri $uri/ /index.html; # 尝试文件，目录，最后是 index.html
    }
}
```

**请求示例及其匹配结果：**

- **`/favicon.ico`**: 匹配 `location = /favicon.ico` (精确匹配)
- **`/static/js/app.js`**: 匹配 `location ^~ /static/` (前缀优先)
- **`/index.php`**: 匹配 `location ~ \.php$` (正则表达式)
- **`/image.JPG`**: 匹配 `location ~* \.(jpg|png)$` (不区分大小写正则表达式)
- **`/api/products`**: 匹配 `location /api/` (普通前缀)
- **`/about`**: 匹配 `location /` (通用匹配)
- **`/static/index.php`**: **注意**！尽管有 `.php` 正则表达式，但 `location ^~ /static/` 优先级更高，会先匹配到 `/static/` 块。

