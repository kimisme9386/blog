---
layout: post
title:  "docker-compose.yml 和 Dockerfile 變數傳遞注意事項"
categories: [blog, docker]
tags: [hot, summer]
---

### 要點說明

- docker-compose.yml 中的 env_file，跟 environment 的功能一樣，並不是可以指定不同的 .env file name，換言之，讀取變數還是只能用 .env

- docker-compose.yml 中 `service name` -> `build` -> `args` 設定，即是 pass 給 Dockerfile 中的 ARG

- Dockerfile 中 `ARG` 遇到 `FROM` 要小心，例如 `FROM` 前就算有宣告過 `ARG VAR1`, FROM 後要再使用，還是需要宣告一次

不正確

```
ARG GITHUB_ACCESS_TOKEN

FROM ubuntu:16.04

RUN composer config -g github-oauth.github.com ${GITHUB_ACCESS_TOKEN} 
```

應修改成

```
FROM ubuntu:16.04

ARG GITHUB_ACCESS_TOKEN
RUN composer config -g github-oauth.github.com ${GITHUB_ACCESS_TOKEN} 
```

- Dockerfile RUN、COPY、ADD 是在 build image 階段執行，CMD、ENTRYPOINT 是在 run container 的階段時候執行

- Dockerfile RUN 中如果想要使用外部變數，例如 ARG，可用變通方法，再宣告一次 ENV foo ${foo}，${foo} 從 ARG 而來

例如

```
FROM php:7.4-fpm-alpine3.10

ARG FOO=${FOO}
ENV FOO ${FOO}
RUN export FOO=${FOO}
```


執行 docker build，使用 --build-arg 帶入
```
ocker build -t {your_name} --build-arg FOO=test .
```


- Dockerfile CMD 中若要使用變數，僅能使用環境變數

例如

```
ARG UBUNTU_VERSION

FROM ubuntu:${UBUNTU_VERSION}

WORKDIR /var/www/htdocs/test_project
COPY . .

CMD bash -c "composer config -g github-oauth.github.com ${GITHUB_ACCESS_TOKEN} && composer install && apachectl -D FOREGROUND"
```

執行 docker run 時，使用 -e 帶入

```
docker run -it --rm -e "GITHUB_ACCESS_TOKEN={your_token}" {your-docker-image-name}
```
