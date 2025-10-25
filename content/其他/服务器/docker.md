# Docker 核心概念与原理

## 容器（container）

容器是 Docker 运行应用程序的独立、轻量级的执行单元。它是一个**运行时实例**，由 Docker 镜像创建。可以把容器想象成一个**精简版的、隔离的操作系统环境**，里面只包含运行特定应用所需的代码、运行时、系统工具、系统库和设置。

**与虚拟机的区别**：

- **虚拟机 (VM)**：
    - 在物理硬件上运行**宿主操作系统 (Host OS)**。
    - 在宿主操作系统上运行**Hypervisor**（如 VMware ESXi, VirtualBox）。
    - Hypervisor 之上运行多个**客户操作系统 (Guest OS)**，每个客户操作系统都有自己的内核、库和二进制文件。
    - **资源消耗大**：每个 VM 都包含完整的操作系统，启动慢，占用大量内存和磁盘空间。
    - **隔离性强**：硬件级别的虚拟化。
    - **启动速度慢**：需要启动整个操作系统。
- **容器 (Container)**：
    - 在物理硬件上运行**宿主操作系统 (Host OS)**。
    - 在宿主操作系统上运行**Docker Engine**。
    - Docker Engine 利用宿主操作系统的**内核**，通过**命名空间 (Namespaces)** 和 **控制组 (Cgroups)** 等技术实现隔离。
    - **轻量级**：共享宿主机的内核，不包含完整的操作系统，只包含应用及其依赖。
    - **资源消耗小**：启动快，占用资源少。
    - **隔离性相对弱**：内核共享，但进程、文件系统、网络等是隔离的。
    - **启动速度快**：秒级甚至毫秒级启动。


**容器的隔离性**：

- **进程隔离**：每个容器都有自己的进程空间，容器内的进程看不到或影响容器外的进程，反之亦然。
- **文件系统隔离**：每个容器都有自己的根文件系统，对文件系统的修改只影响当前容器。
- **网络隔离**：每个容器有自己的网络接口、IP 地址和端口空间。
- **资源隔离**：通过 Cgroups，可以限制容器使用的 CPU、内存、I/O 等资源。

## 镜像（Image）

镜像是**只读的模板**，用于创建 Docker 容器。它包含了运行应用程序所需的所有内容：代码、运行时、库、环境变量和配置文件。可以把镜像看作是一个类 (Class)，而容器是这个类的一个实例 (Instance)。

**镜像的分层存储 (Union File System)**：

- Docker 镜像不是一个单一的巨大文件，而是由一系列**只读层 (Layers)** 组成的。
- 每一层都代表一个 Dockerfile 中的指令（例如 `RUN`, `COPY`, `ADD`）。
- 当一个镜像被创建时，它会基于一个**基础镜像 (Base Image)**，并在其上叠加新的层。
- 这种分层机制带来了巨大的优势：
	- **节省空间**：不同的镜像可以共享相同的底层。
	- **提高构建速度**：如果某一层没有改变，Docker 可以直接使用缓存的层，而不需要重新构建。
	- **版本控制**：每一层都是一个小的增量，方便回溯和管理。
- 当容器启动时，Docker 会在镜像层的顶部添加一个**可写层 (Writable Layer)**。所有对容器文件系统的修改（例如创建文件、修改文件）都会发生在这个可写层中。当容器被删除时，这个可写层也会被删除，而底层的镜像层保持不变。

**镜像的构建过程 (Dockerfile)**：

- 镜像通常通过编写 `Dockerfile` 来构建。`Dockerfile` 是一个包含一系列指令的文本文件，这些指令告诉 Docker 如何一步步构建镜像。
- 每个指令都会在镜像中创建一个新的层。
- 例如：`FROM ubuntu:latest` (指定基础镜像), `COPY . /app` (复制文件), `RUN apt-get update` (执行命令), `CMD ["nginx", "-g", "daemon off;"]` (容器启动时执行的命令)。

**镜像的标签 (Tag)**：

- 标签用于标识镜像的不同版本或变体。
- 例如：`ubuntu:latest` (最新版本), `ubuntu:20.04` (特定版本), `my-app:v1.0`。
- `latest` 是一个常用的标签，但并不总是指最新发布的版本，而是指最近构建或推送的版本。

# 仓库 (Registry)

仓库是用于**存储和分发 Docker 镜像**的服务。它类似于代码的版本控制系统（如 Git），但专门用于管理镜像。

**Docker Hub**：

- Docker 官方提供的公共仓库，是默认的镜像仓库。
- 包含了大量的官方镜像（如 Ubuntu, Nginx, MySQL）和社区用户共享的镜像。
- 可以通过 `docker pull <image_name>` 从 Docker Hub 拉取镜像，通过 `docker push <image_name>` 推送自己的镜像。

**私有仓库**：

- 企业或个人可以搭建自己的私有仓库，用于存储不对外公开的镜像。
- 常见的私有仓库解决方案有：Docker Registry (官方开源), Harbor, Nexus Repository 等。
- 使用私有仓库可以更好地控制镜像的安全性、访问权限和分发效率。

## 数据卷 (Volume)

Docker 容器是基于镜像创建的，并且在镜像层之上有一个**可写层**。当容器被删除时，这个可写层也会被销毁，这意味着容器内部产生的所有数据（例如数据库文件、日志文件、用户上传的文件）都会**丢失**。

这在很多场景下是不可接受的：

- **数据库**：如果数据库容器被删除，所有数据都将丢失。
- **日志**：容器重启或删除后，日志文件会消失，不利于故障排查。
- **配置**：应用程序的配置可能需要外部管理，而不是硬编码在镜像中。
- **用户上传文件**：网站或应用的用户上传内容需要持久化。

**数据卷 (Volume) 的核心目的就是解决容器数据的持久化问题。** 它提供了一种机制，可以将宿主机上的目录或文件挂载到容器内部，从而实现容器内外的数据共享和持久存储。当容器被删除时，数据卷中的数据仍然保留在宿主机上，可以被新的容器重新挂载使用。

**数据卷的类型**：

1. **具名卷 (Named Volumes)**
	- **特点**：由 Docker 引擎管理，具有一个唯一的名称。Docker 会在宿主机上的特定位置（通常是 `/var/lib/docker/volumes/` 下）创建和管理这些卷。
	- **优点**：
	    - **易于管理**：通过名称引用，方便在多个容器之间共享。
	    - **与宿主机路径解耦**：你不需要关心数据实际存储在宿主机的哪个具体路径下，Docker 会帮你管理。
	    - **可移植性**：在不同宿主机上使用具名卷，Docker 会自动处理存储位置。
	    - **备份和迁移方便**：可以通过 Docker 命令进行备份和迁移。
	- **使用场景**：
		- 数据库数据存储。
		- 日志文件存储。
		- 需要持久化且由 Docker 管理的数据。
	- **创建与使用**：
	    - **创建具名卷**：`docker volume create my_data_volume`
	    - **运行容器并挂载**：`docker run -d --name my_db -v my_data_volume:/var/lib/mysql mysql:latest`
	        - `-v my_data_volume:/var/lib/mysql`：将名为 `my_data_volume` 的数据卷挂载到容器内的 `/var/lib/mysql` 路径。
		- **查看具名卷**：`docker volume ls`
		- **检查具名卷详情**：`docker volume inspect my_data_volume`
		- **删除具名卷**：`docker volume rm my_data_volume` (只有在没有容器使用时才能删除)
2. **匿名卷 (Anonymous Volumes)**
	- **特点**：没有显式名称，当容器创建时 Docker 会自动为其创建一个唯一的 ID 作为名称。当容器被删除时，如果匿名卷没有被其他容器使用，Docker 默认会删除它（但可以通过配置改变行为）。
	- **优点**：
	    - **简单**：无需预先创建，直接在 `docker run` 命令中指定。
	- **缺点**：
	    - **难以管理**：由于没有名称，很难在多个容器之间共享或引用。
	    - **数据丢失风险**：容器删除时可能导致数据丢失。
	- **使用场景**：
	    - 临时数据，或者你明确知道数据不需要持久化到容器生命周期之外。
	    - 通常不推荐用于重要数据的持久化。
	- **使用**：
	    - `docker run -d --name my_app -v /app/data my_image`
	        - `-v /app/data`：Docker 会自动创建一个匿名卷并挂载到容器内的 `/app/data` 路径。
3. **绑定挂载 (Bind Mounts)**
	- **特点**：将宿主机上的**任意指定路径**（文件或目录）直接挂载到容器内部的指定路径。
	- **优点**：
	    - **完全控制**：你可以完全控制宿主机上的数据存储位置。
	    - **方便开发**：在开发过程中，可以直接修改宿主机上的代码，容器内会立即反映，无需重新构建镜像。
	    - **配置文件管理**：将宿主机上的配置文件挂载到容器内。
	- **缺点**：
	    - **依赖宿主机路径**：如果宿主机路径不存在，Docker 会自动创建。
	    - **可移植性差**：如果将容器迁移到另一台宿主机，需要确保目标宿主机上也有相同的路径和数据。
	    - **安全性风险**：如果挂载了敏感的宿主机目录，容器可能会访问到不应该访问的文件。
	- **使用场景**：
	    - **开发环境**：实时代码同步。
	    - **配置文件**：将宿主机上的配置文件挂载到容器内。
	    - **宿主机日志收集**：将宿主机的日志目录挂载到日志收集容器。
	- **使用**：
	    - `docker run -d --name my_web -v /path/on/host:/path/in/container my_nginx_image`
	        - `-v /home/user/my_website:/usr/share/nginx/html`：将宿主机 `/home/user/my_website` 目录挂载到 Nginx 容器的 `/usr/share/nginx/html` 目录。
	    - **挂载单个文件**：`docker run -d --name my_conf -v /host/app.conf:/etc/app/app.conf my_app_image`

## 网络 (Networking)

Docker 网络允许容器之间以及容器与外部世界进行通信。理解 Docker 网络模式对于构建分布式应用至关重要。

**Docker 默认网络模式**：

1. **`bridge` 模式** (默认)
	- **工作原理**：
	    1. Docker 在宿主机上创建一个名为 `docker0` 的虚拟网桥。
	    2. 每个容器都会分配一个虚拟网卡 (veth pair)，一端连接到容器内部，另一端连接到 `docker0` 网桥。
	    3. 容器通过 `docker0` 网桥与宿主机和其他容器进行通信。
	    4. 如果容器需要与外部网络通信，`docker0` 会通过 NAT (网络地址转换) 机制将容器的流量转发到宿主机的物理网卡。
	- **使用**：这是默认模式，无需额外指定。
	    - `docker run -d --name my_app my_image`
2. `host` 模式
	- **特点**：
	    - 容器**不拥有独立的网络栈**，而是直接使用宿主机的网络栈。
	    - 容器内的端口直接映射到宿主机的端口，无需端口映射。
	    - **没有网络隔离**：容器可以直接访问宿主机的所有网络接口和端口。
	- **优点**：
	    - **性能高**：没有额外的网络层，减少了网络开销。
	- **缺点**：
	    - **端口冲突**：如果多个容器都使用 `host` 模式，且监听相同的端口，就会发生端口冲突。
	    - **安全性低**：容器直接暴露在宿主机网络上。
	- **使用场景**：
	    - 对网络性能要求极高，且能接受端口冲突和安全风险的场景。
	    - 需要容器直接访问宿主机网络服务的场景。
	- **使用**：
	    - `docker run -d --name my_app --network host my_image`
3. `none` 模式
	- **特点**：
	    - 容器**没有网络接口**，完全隔离，无法与外部或宿主机通信。
	- **使用场景**：
	    - 特殊用途，例如只需要进行计算，不需要网络连接的容器。
	    - 作为自定义网络配置的基础。
	- **使用**：
	    - `docker run -d --name my_app --network none my_image`

# Docker 常用命令

- `docker run`：运行容器。
- `docker ps`：查看运行中的容器。
- `docker images`：查看本地镜像。
- `docker build`：构建镜像。
- `docker pull`：拉取镜像。
- `docker push`：推送镜像。
- `docker stop/start/restart/rm`：停止/启动/重启/删除容器。
- `docker rmi`：删除镜像。
- `docker exec`：在运行中的容器中执行命令。
- `docker logs`：查看容器日志。
- `docker network`：管理 Docker 网络。
- `docker volume`：管理 Docker 数据卷。
- `docker system prune`：清理 Docker 资源。

# Docker Compose

Docker Compose 是一个用于定义和运行多容器 Docker 应用程序的工具。通过一个 YAML 文件来配置应用程序的服务，然后使用一个命令，就可以创建并启动所有服务。

**核心概念：**

1. **服务 (Services)：**
    
    - 服务是应用程序中的一个独立组件，例如一个 Web 服务器、一个数据库或一个缓存。
    - 在 `docker-compose.yml` 文件中，每个服务都定义了其使用的 Docker 镜像、端口映射、卷挂载、环境变量等配置。
2. **项目 (Project)：**
    
    - 一个项目通常对应一个应用程序，它包含一个或多个服务。
    - 当你运行 `docker-compose up` 命令时，Docker Compose 会根据 `docker-compose.yml` 文件中的定义创建一个项目。
3. **`docker-compose.yml` 文件：**
    
    - 这是 Docker Compose 的核心配置文件。
    - 它使用 YAML 格式，定义了应用程序的所有服务、网络和卷。
    - 一个典型的 `docker-compose.yml` 文件会包含 `version`、`services`、`networks` 和 `volumes` 等顶级键。

**优势：**

1. **简化多容器应用管理：**
    
    - 对于包含多个服务（例如 Web 应用、数据库、缓存等）的复杂应用，手动管理每个 Docker 容器会非常繁琐。
    - Docker Compose 允许你将所有服务的配置集中在一个文件中，然后通过一个命令来启动、停止、重启或删除整个应用。
2. **环境一致性：**
    
    - 通过 `docker-compose.yml` 文件，你可以确保开发、测试和生产环境中的应用程序配置保持一致。
    - 这减少了“在我机器上可以运行”的问题，因为所有团队成员都使用相同的配置来运行应用程序。
3. **快速开发和测试：**
    
    - 开发者可以快速启动一个完整的应用程序环境，而无需手动配置每个服务。
    - 这对于本地开发和自动化测试非常有用。
4. **易于扩展和维护：**
    
    - 当需要添加或修改服务时，只需更新 `docker-compose.yml` 文件即可。
    - Docker Compose 会自动处理容器的创建、网络连接和卷挂载。
5. **服务间通信：**
    
    - Docker Compose 会为项目中的所有服务创建一个默认网络，使得服务之间可以通过服务名进行通信，而无需知道对方的 IP 地址。

**一个简单的 `docker-compose.yml` 示例：**

```yaml
version: '3.8' # 指定 Compose 文件格式版本

services:
  web: # 定义一个名为 'web' 的服务
    image: nginx:latest # 使用 Nginx 镜像
    ports:
      - "80:80" # 将主机的 80 端口映射到容器的 80 端口
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro # 挂载本地 nginx.conf 到容器
    depends_on: # 声明 web 服务依赖于 db 服务
      - db

  db: # 定义一个名为 'db' 的服务
    image: postgres:13 # 使用 PostgreSQL 13 镜像
    environment: # 设置环境变量
      POSTGRES_DB: mydatabase
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
    volumes:
      - db_data:/var/lib/postgresql/data # 挂载一个命名卷用于数据持久化

volumes:
  db_data: # 定义一个命名卷
```

**如何使用：**

1. **安装 Docker 和 Docker Compose。**
2. **创建一个 `docker-compose.yml` 文件**，并定义你的服务。
3. **在包含 `docker-compose.yml` 文件的目录下，运行以下命令：**
    - `docker-compose up -d`：在后台启动所有服务。
    - `docker-compose ps`：查看服务状态。
    - `docker-compose stop`：停止所有服务。
    - `docker-compose down`：停止并删除所有服务、网络和卷。

# 如何自己打包docker

