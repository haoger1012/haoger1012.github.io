---
title: Docker 建立 MariaDB 中文亂碼問題
categories:
- Container 
---

使用 Docker 建立 MariaDB 時想預設先塞資料進去，於是寫了一份 Dockerfile

```dockerfile
FROM mariadb
COPY script.sql /docker-entrypoint-initdb.d/
ENV MYSQL_ROOT_PASSWORD 12345 # root 密碼先預設為 12345
EXPOSE 3306
```

`script.sql` 的內容如下，建立一個 `test` 資料庫和一張 `TEST_TABLE` 的資料表，再塞入一筆測試資料

```sql
CREATE DATABASE test;
USE test;

CREATE TABLE TEST_TABLE (
    ID int NOT NULL AUTO_INCREMENT,
    NAME varchar(64) CHARACTER SET utf8 NULL,
    CONSTRAINT PK_TEST_TABLE PRIMARY KEY (ID)
);

INSERT INTO TEST_TABLE (ID, NAME)
VALUES (1, '測試');
```

首先建立 Docker image

```bash
docker build . -t test-db
```

再將 container 跑起來

```bash
docker run -it -d --name test-db -p 3306:3306 test-db
```

到資料庫查詢時，沒想到出現中文亂碼

![image](https://user-images.githubusercontent.com/46283957/149251320-d3b4782e-282e-4f62-8669-7268dfbf0a31.png)

後來找了一篇解法 (參考相關連結)，多新增一個環境變數 `LANG=C.UTF-8` 即可解決

```dockerfile
FROM mariadb
COPY script.sql /docker-entrypoint-initdb.d/
ENV LANG=C.UTF-8
ENV MYSQL_ROOT_PASSWORD 12345
EXPOSE 3306
```

![image](https://user-images.githubusercontent.com/46283957/149251355-2c5d5b19-e143-46c4-a1da-819aba7a8541.png)

### 相關連結

- [UTF8 Encoded SQL Scripts in initdb](https://github.com/docker-library/mysql/issues/131#issuecomment-248412170)
