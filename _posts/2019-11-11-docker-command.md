---
title: Docker 常用指令
categories:
- Container 
---
### 常用指令

#### Image

| 指令 | 用途 |
| -------- | -------- |
| [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) | 下載映像檔 |
| [docker push](https://docs.docker.com/engine/reference/commandline/push/) | 上傳映像檔 |
| [docker build](https://docs.docker.com/engine/reference/commandline/build/) | 依 [Dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) 建立映像檔 |
| [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) | 映像檔加上 tag，可變更映像檔名稱 |
| [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) | 刪除映像檔 |

#### Container

| 指令 | 用途 |
| -------- | -------- |
| [docker ps](https://docs.docker.com/engine/reference/commandline/ps/) | 列出執行中的容器 |
| [docker run](https://docs.docker.com/engine/reference/commandline/run/) | 執行容器 |
| [docker start](https://docs.docker.com/engine/reference/commandline/start/) | 啟動容器 |
| [docker attach](https://docs.docker.com/engine/reference/commandline/attach/) | 進入容器 (進入容器後，不想關閉容器，可打 Ctrl + P、Ctrl + Q) |
| [docker stop](https://docs.docker.com/engine/reference/commandline/stop/) | 關閉容器 |
| [docker rm](https://docs.docker.com/engine/reference/commandline/rm/) | 刪除容器 |
| [docker exec](https://docs.docker.com/engine/reference/commandline/exec/) | 容器執行其他應用程式 |
| [docker cp](https://docs.docker.com/engine/reference/commandline/cp/) | 複製檔案至容器 |
| [docker commit](https://docs.docker.com/engine/reference/commandline/commit/) | 將目前容器建立成一個新的映像檔 |
| [docker inspect](https://docs.docker.com/engine/reference/commandline/inspect/) | 查看容器或映像檔的資訊 |

### 指令參數使用範例

1. 列出所有容器

    ```bash
    docker ps -a
    ```

    - `-a`：列出所有容器 (若不加此參數，預設只有執行中的容器)

2. 執行容器

    ```bash
    docker run -it --name my-app --rm -d -p 5566:80 --entrypoint="cmd" -v D:\Repos\Test:C:inetpub/wwwroot IMAGE [COMMAND]
    ```

    - `-it`：與容器終端機互動
    - `--name` 給容器取名稱
    - `-rm`：主應用程式執行完畢刪除容器
    - `-d`：背景執行
    - `-p`：host 的 port 與容器內的 port 對映
    - `--entrypoint="cmd"`：設定程式進入點
    - `-v`：host 的檔案路徑對映到容器內檔案路徑

3. 刪除映像檔、容器

    ```bash
    docker rmi -f IMAGE
    docker rm -f CONTAINER
    ```

    - `-f`：強制刪除

4. 容器執行其他應用程式

    ```bash
    docker exec -it CONTAINER COMMAND
    ```

    - `-it`：與容器終端機互動

5. 依 Dockerfile 建立映像檔

    ```bash
    docker build -f Dockerfile.test -t my-app . --no-cache
    ```

    - `-f`：Dockerfile 的名稱 (若不加此參數，預設是 Dockerfile)
    - `-t`：Dockerfile 建立的映像檔名稱
    - `--no-cache`：建立映像檔時不使用 cache

### 相關連結

- [docker (base command)](https://docs.docker.com/engine/reference/commandline/docker/)
