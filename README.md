<div align="center">

<img src="assets/logo.png" width="120" alt="UPilot" />

# UPilot

115 网盘自动化上传与 qBittorrent 联动工具

[![build](https://github.com/xushier/UPilot/actions/workflows/docker-build.yml/badge.svg)](https://github.com/xushier/UPilot/actions/workflows/docker-build.yml)

</div>

UPilot 监控本地媒体目录（如刮削软件整理后的媒体库），发现新文件自动上传到 115 网盘，上传完成后把状态回写到 qBittorrent 种子标签，并按照配置的规则自动清理本地文件和种子。配合 MoviePilot / NAStool 与 OneStrm 等工具，可以串起一套完整的流程：

> qBittorrent 下载 → MoviePilot / NAStool 整理刮削 → **UPilot 监控上传 115 + 标签回写** → 按规则自动清理 → OneStrm / Emby 302 直接播放

## 特色

**秒传优先，重试不烧流量、不费硬盘**

- 优先 SHA1 秒传，网盘里已有同 hash 文件时瞬间完成，不消耗上行带宽；
- 秒传失败会自动重试，重试只重发校验请求，不需要重新上传数据；多次失败后才转入普通上传（断点续传，失败自动重试）；
- SHA1 计算结果持久保存，任务重试、程序重启都不会重复计算，同一个文件反复扫描也不会反复读盘，对硬盘友好。

**分享与直链**

- 支持网盘内文件/文件夹的单个分享和批量分享，分享历史可查、可一键清空；
- 一键获取文件下载直链，临时下载个东西不用再打开网盘客户端或借助其他程序。

**离线转存**

- 支持分享链接、磁力链接的解析、预览与批量转存，转存历史可直接复用为目标目录。

**QB 标签回写**

- 上传进度实时回写到 qBittorrent 种子标签（处理中 / 上传中 / 已上传 / 缺文件数等），每个种子传没传完、每个文件在什么状态一目了然，配合自动清理不会误删没传完的种子。

**自动清理**

- 本地文件与 QB 种子自动清理，支持多种清理模式；
- 可按关键字给不同种子设置不同的 H&R（做种时长）要求，达标才清理；
- 支持错种清理（自动识别 register、anned、音轨、压制等坏种关键字并清理）。

## 建议搭配

| 用途 | 软件 |
| --- | --- |
| 下载与整理刮削 | [MoviePilot](https://github.com/jxxghp/MoviePilot) / [NAStool](https://github.com/NAStool/nas-tools)（已停止维护，老用户可继续使用） |
| 302 直链播放 | [OneStrm](https://wiki.onestrm.cn/) 等 302 反代工具，配合 Emby 生成 strm 直接播放 |

## 快速开始

```yaml
services:
  upilot:
    image: xushier/upilot:latest
    container_name: upilot
    restart: unless-stopped
    network_mode: bridge
    ports:
      - "9843:9843"
    environment:
      - TZ=Asia/Shanghai
    volumes:
      # 配置文件目录
      - ./config:/config
      # 授权绑定宿主机（激活必需，容器重建/升级不影响授权）
      - /etc/machine-id:/etc/machine-id:ro
      # 刮削软件整理后的媒体目录
      - /path/to/b-media:/media/b-media
      # qBittorrent 下载目录
      - /path/to/a-download:/media/a-download
    ulimits:
      nofile:
        soft: 65536
        hard: 65536
```

- 首次访问 `http://ip:9843`，默认账号密码均为 `admin`，登录后请及时修改；

## 致谢

- [p115client](https://github.com/ChenyangGao/p115client)；
- [FastAPI](https://github.com/fastapi/fastapi)、[SQLAlchemy](https://github.com/sqlalchemy/sqlalchemy)；
- [React](https://github.com/facebook/react)、[shadcn/ui](https://github.com/shadcn-ui/ui)、[Tailwind CSS](https://github.com/tailwindlabs/tailwindcss)；
- qBittorrent WebUI API。

## 授权说明

本软件需授权使用，新安装可试用 3 天，到期后导入激活码继续使用（支持限时/永久授权，可绑定设备）。

获取激活码请扫码添加作者微信，发送设备指纹：

<div align="center">

<img src="assets/wechat.png" width="200" alt="作者微信二维码" />

</div>
