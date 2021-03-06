# Docker的若干种交付方式

by HyperQing 2018-05-12

[TOC]

Docker的使用方法是多种多样的，可以按照实际需要来调整使用方式。下列是一些常见的Docker最终交付形式。

每种方式的标题是作者自行概括，不是业内惯例，如果有更好的描述方式，还请告知。

## 常用基础镜像

- `webdevops/php-nginx` Nginx + PHP 7 基础镜像。镜像已包含几乎所有 PHP 扩展（生产实践中未发现有缺失扩展）。 http://dockerfile.readthedocs.io/en/latest/content/DockerImages/dockerfiles/php-nginx.html
- `mysql` MySQL数据库的镜像。优先使用5.7或以上版本，原生支持JSON。
- `redis` Redis分布式数据库的镜像。使用最新版本即可。
- `nginx` Nginx镜像，用于单独使用时，通常用于静态网站、反向代理或负载均衡。

## 三方工具

- 阿里云腾讯云的 Docker Swarm 或 Kubernetes(K8S)。
- DaoCloud，容器云平台。使用该平台即可实现可视化的、版本化的持续构建和持续交付。

## 普通单机镜像

将代码应用打包到镜像中。

特性如下：

- 适用于最终向客户交付产品，或可持续稳定更新产品。
- 应用代码和数据库分离为两个容器，独立运行（因此可独立重启和更新容器）。
- 应用代码可以单独升级，且每次都进行镜像构建，升级文件版本稳定可靠100%，不存在差错（速度较慢）。
- 代码存在于镜像中，安全性好，不会意外修改或丢失代码。
- 将多台服务器添加到应用集群后，即可轻松完成大批量的服务器的应用容器更新。

## 快速单机镜像

镜像仅包含运行环境，代码以挂载 Volume 的方式运行。

特性如下：

- 适用于快速迭代开发、一天发布几十个版本、尚未形成应用集群的产品。
- 应用代码和数据库分离为两个容器，独立运行（因此可独立重启和更新容器）。
- 应用代码使用Git来更新，通过Git标签实现版本化管理，以挂载的方式运行代码。（避免重复构建镜像，节约时间）
- 代码存放于宿主机，安全性一般，容易因为人为操作失误，如Git操作失误等导致代码丢失或差错。
- 因为Git无法为应用集群更新代码，如果有多台服务器，则需要手工进入多台服务器执行命令来更新代码（或通过钩子自动更新）。

## 多网站共享镜像

镜像仅包含运行环境，同时挂载多份代码。

特性如下：

- 适合团队开发，多个网站项目共存，充分利用测试服务器资源。
- 应用代码和数据库分离为两个容器，独立运行（因此可独立重启和更新容器）。
- 以挂载方式，挂载多个网站目录，网站目录可用IDE同步功能来同步代码（更新速度最快，但最不靠谱，经常无法保证全部文件都是最新的）。

多个网站共享一个MySQL实例。

## 负载均衡可扩展镜像方案

每个应用独享一个应用镜像，使用Nginx可进行反向代理或负载均衡，将流量分发到不同应用或同一个应用的多台主机。

特性如下：

- 适合生产环境发布上线，配合Docker管理工具可轻松实现应用集群。
