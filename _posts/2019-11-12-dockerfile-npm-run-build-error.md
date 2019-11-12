---
title: Dockerfile 執行 npm run build 時發生錯誤
categories:
- Container 
---
這是將 Angular 專案建立成 Docker image 的 Dockerfile

```dockerfile
FROM node:10.17.0 AS node-builder
WORKDIR /src
COPY . .
RUN npm install
RUN npm run build 

FROM nginx:alpine
COPY --from=node-builder /src/dist/my-app /usr/share/nginx/html
```

執行 `docker build` 時，在 Linux container 可以順利完成，但在 Windows container 跑到 `npm run build` 就掛了，會出現以下訊息：

```txt
Step 5/7 : RUN npm run build
 ---> Running in e9c6863fe498

> my-app@0.0.0 build /src
> ng build

Killed
npm ERR! code ELIFECYCLE
npm ERR! errno 137
npm ERR! my-app@0.0.0 build: `ng build`
npm ERR! Exit status 137
npm ERR!
npm ERR! Failed at the my-app@0.0.0 build script.
npm ERR! This is probably not a problem with npm. There is likely additional logging output above.
 
npm ERR! A complete log of this run can be found in:
npm ERR! /root/.npm/_logs/2019-11-11T11_11_11_111Z-debug.log
The command '/bin/sh -c npm run build' returned a non-zero code: 137
```

找了很多相關資料指出是記憶體不足所導致的問題，Window container 預設記憶體只有 1 GB，所以在 `docker build` 時多加 `-m` 參數將記憶體加大，問題解決

```bash
docker build -t my-app -m 2g .
```

### 相關連結

- [Fixing error code 137 when building a Docker image](https://alex-v.blog/2018/11/11/fixing-error-code-137-when-building-a-docker-image/)
- [npm run build fails when using in a docker container](https://github.com/strapi/strapi-docker/issues/125)
- [Docker windows container memory limit](https://stackoverflow.com/questions/43460770/docker-windows-container-memory-limit)
