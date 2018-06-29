# debian-ruby-jemalloc
ruby with jemalloc based on debian-stretch

### 说明
本镜像基于debian:stretch 制作，更换了ruby内存管理为jemalloc.此dockerfile 本地构建完成后直接push到
docker hub 上（astarup账号）.

### 克隆到本地，build，push

```bash
git clone https://github.com/zhulux/debian-ruby-jemalloc.git

cd debian-ruby-jemalloc
docker build -t debian-stretch:ruby2.5.1
```

打tag

```bash
docker tag debian-stretch:ruby2.5.1 astarup/debian-stretch:ruby2.5.1
```

login and push to dockerhub

```bash
docker login 

docker push astarup/debian-stretch:ruby2.5.1
```
