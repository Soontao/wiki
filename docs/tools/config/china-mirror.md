# 使用中国镜像

Note: 在国内, 因为种种原因, 通过maven/npm/yarn/apt的下载速度很慢, 所以才有了镜像这一说法. 下面的配置是如何配置中国的镜像

## Maven

![](https://res.cloudinary.com/digf90pwi/image/upload/v1504269471/3d114bace0a104fd19fb42433316dac5_wnrxy3.png)

这里有一篇[文章](https://yq.aliyun.com/articles/46991), 详细的讲述了如何配置镜像

首先就是找到**Maven的安装路径**, 修改**conf目录**中的```settings.xml```, 将mirrors节点做如下修改

```xml
<mirrors>
   <mirror>
        <id>nexus-aliyun</id>
        <mirrorOf>*</mirrorOf>
        <name>Nexus aliyun</name>
        <url>http://maven.aliyun.com/nexus/content/groups/public</url>
    </mirror>
</mirrors>
```

## npm/yarn

![](https://res.cloudinary.com/digf90pwi/image/upload/v1504269378/7155aa40d0d8bd4619cd63e30dd68dcd_w7urk7.jpg)

npm/yarn作为前端必备工具, 在国内也是慢的要死

依旧是使用[Taobao的镜像](https://npm.taobao.org/), 运行如下命令即可

`For NPM`

```bash
npm config set registry https://registry.npm.taobao.org/
```

`For YARN`

```bash
yarn config set registry https://registry.npm.taobao.org/
```

### electron and node sass

![](https://res.cloudinary.com/digf90pwi/image/upload/v1504269521/e5d5c1fa84d291555ac48667bfd83cf5_gkngzy.jpg)

`For electron project`

* CMD

```bash
set SASS_BINARY_SITE=https://npm.taobao.org/mirrors/node-sass/
set ELECTRON_MIRROR=http://npm.taobao.org/mirrors/electron/
```

* BASH

```bash
export ELECTRON_MIRROR="https://npm.taobao.org/mirrors/electron/"
export SASS_BINARY_SITE="https://npm.taobao.org/mirrors/node-sass"
```

## pypi

![](https://res.cloudinary.com/digf90pwi/image/upload/v1504269379/8ecffe876081e72639603dae6ad548fb_mrc1de.jpg)

pip是python的包管理器，可惜通常情况下，下载速度都太慢了，可以换用ustc的镜像

`~/.pip/pip.conf`

```text
[global]
index-url = https://mirrors.ustc.edu.cn/pypi/web/simple
format = columns
```

## package manager

[mirrors.ustc.edu.cn](http://mirrors.ustc.edu.cn/), 是中国科技大学的镜像网站, 里面包含了很多linux发行版的镜像, 以及很多包管理器的镜像.

在[这个网址](http://mirrors.ustc.edu.cn/help/)可以看到如何配置各个包管理器的源

比较有用的

### Docker

![](https://res.cloudinary.com/digf90pwi/image/upload/v1504269550/070b1f147c314a8974002d0ff7eade31_gp7uzc.png)

`/etc/docker/daemon.json`

```json
{
  "registry-mirrors": ["https://mirrors.ustc.edu.cn/dockerhub/"]
}
```

`DaoCloud`

```bash
curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://ac96d588.m.daocloud.io
```

重启docker即可生效

### ubuntu

![](https://res.cloudinary.com/digf90pwi/image/upload/v1504269573/82a46fab2706ee355cafa991319f240a_j1fmhw.jpg)

以16.04为例

`/etc/apt/source.list`

```conf
deb http://mirrors.ustc.edu.cn/ubuntu/ xenial main restricted universe multiverse
deb-src http://mirrors.ustc.edu.cn/ubuntu/ xenial main restricted universe multiverse

deb http://mirrors.ustc.edu.cn/ubuntu/ xenial-security main restricted universe multiverse
deb-src http://mirrors.ustc.edu.cn/ubuntu/ xenial-security main restricted universe multiverse

deb http://mirrors.ustc.edu.cn/ubuntu/ xenial-updates main restricted universe multiverse
deb-src http://mirrors.ustc.edu.cn/ubuntu/ xenial-updates main restricted universe multiverse

deb http://mirrors.ustc.edu.cn/ubuntu/ xenial-backports main restricted universe multiverse
deb-src http://mirrors.ustc.edu.cn/ubuntu/ xenial-backports main restricted universe multiverse

## Not recommended
# deb http://mirrors.ustc.edu.cn/ubuntu/ xenial-proposed main restricted universe multiverse
# deb-src http://mirrors.ustc.edu.cn/ubuntu/ xenial-proposed main restricted universe multiverse
```

使用[配置生成器](https://mirrors.ustc.edu.cn/repogen/)会更加方便