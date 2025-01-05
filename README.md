# 自动化媒体管理系统

这是一个基于 Docker 的完整媒体管理系统，包含下载、整理、字幕、播放等全套功能。

## 系统组件

- **qBittorrent** (端口: 8080): BT 下载工具
- **Sonarr** (端口: 8989): 电视剧自动下载和管理
- **Radarr** (端口: 7878): 电影自动下载和管理
- **Prowlarr** (端口: 9696): 种子索引器管理
- **ChineseSubFinder** (端口: 19035): 中文字幕下载
- **Bazarr** (端口: 6767): 多语言字幕下载
- **Jellyfin** (端口: 8096): 媒体服务器
- **Overseerr** (端口: 5055): 媒体请求系统

## 安装步骤

1. 安装必要的软件：

   - 安装 [Docker Desktop](https://www.docker.com/products/docker-desktop/)
   - 确保 Docker Compose 已安装

2. 准备存储：

   - 确保有足够的存储空间
   - 创建并设置媒体目录权限：

   ```bash
   # 创建媒体目录
   sudo mkdir -p /Volumes/media/{downloads,tv,movies}
   sudo chown -R $(id -u):$(id -g) /Volumes/media/{downloads,tv,movies}
   ```

   > 注意：在 macOS 上可能会看到一些系统文件（如 .Spotlight-V100）的权限错误，这是正常的，可以忽略

   - 创建并设置配置目录权限：

   ```bash
   # 创建配置目录
   mkdir -p ./config
   chmod -R 777 ./config  # 确保 Docker 容器有足够的权限
   ```

3. 启动服务：
   ```bash
   docker compose up -d
   ```

## 目录结构

```
/Volumes/media/
├── downloads/    # 下载文件存放目录
├── tv/          # 电视剧媒体库
└── movies/      # 电影媒体库

./config/        # 各服务配置文件目录
```

## 初始设置

1. **qBittorrent**

   - 访问 http://localhost:8080
   - 默认用户名: admin
   - 默认密码: adminadmin

2. **Prowlarr**

   - 访问 http://localhost:9696
   - 添加你的下载源
   - 配置与 Sonarr/Radarr 的连接

3. **Sonarr/Radarr**

   - Sonarr: http://localhost:8989
   - Radarr: http://localhost:7878
   - 配置下载器（qBittorrent）
   - 配置媒体库路径

4. **Jellyfin**

   - 访问 http://localhost:8096
   - 按向导设置用户和媒体库

5. **ChineseSubFinder/Bazarr**

   - ChineseSubFinder: http://localhost:19035
   - Bazarr: http://localhost:6767
   - 配置字幕源和媒体库路径

6. **Overseerr**
   - 访问 http://localhost:5055
   - 配置与 Jellyfin 的连接
   - 设置用户权限

## 使用流程

1. 在 Overseerr 中搜索和请求想看的电影/剧集
2. Sonarr/Radarr 会通过 Prowlarr 搜索资源并使用 qBittorrent 下载
3. 下载完成后自动整理到对应的媒体库
4. ChineseSubFinder/Bazarr 自动下载匹配的字幕
5. 在 Jellyfin 中观看影片

## 注意事项

1. 所有服务使用 root 权限运行（PUID=0, PGID=0）
2. 确保所有目录有正确的读写权限
3. 首次使用时需要分别配置各个服务
4. 建议设置反向代理以实现远程访问
5. macOS 用户建议使用 Jellyfin 的客户端而不是 Docker 版本，以获得更好的硬件加速支持
