---
layout: post
title:  "滋生工程師的測試頁"
categories: [blog, docker]
tags: [hot, summer]
---

# docker-compose.yml 和 Dockerfile 變數傳遞注意事項

## 要點說明

- docker-compose.yml 中 `service name` -> `build` -> `args`，即是 Dockerfile 中的 ARG

- Dockerfile 中 `ARG` 遇到 `FROM` 要小心，例如 `FROM` 前就算有宣告過 `ARG VAR1`, FROM 後要再使用，還是需要宣告一次



看個例子
