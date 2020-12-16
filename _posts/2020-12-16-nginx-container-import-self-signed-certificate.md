---
title: Nginx 容器安裝自簽憑證
categories:
- Container 
---

### 產生自簽憑證

產生自簽憑證的方式參考保哥的[如何使用 OpenSSL 建立開發測試用途的自簽憑證 (Self-Signed Certificate)
](https://blog.miniasp.com/post/2019/02/25/Creating-Self-signed-Certificate-using-OpenSSL)

- 若要產生名為 `example.com` 的自簽憑證，`ssl.conf` 設定檔如下

    ```ini
    [req]
    prompt = no
    default_md = sha256
    default_bits = 2048
    distinguished_name = dn
    x509_extensions = v3_req

    [dn]
    C = TW
    ST = Taiwan
    L = Taipei
    O = Duotify Inc.
    OU = IT Department
    emailAddress = admin@example.com
    CN = example.com

    [v3_req]
    subjectAltName = @alt_names

    [alt_names]
    DNS.1 = *.localhost
    DNS.2 = localhost
    DNS.3 = example.com
    IP.1 = 192.168.2.100
    ```

    其中 `[dn]` 中的 `CN` 要填入 domain name，`[alt_names]` 要新增 domain name

- 產生**私密金鑰** (`server.key`) 與**憑證檔案** (`server.crt`)

    ```bash
    openssl req -x509 -new -nodes -sha256 -utf8 -days 3650 -newkey rsa:2048 -keyout server.key -out server.crt -config ssl.conf
    ```

### 建立 Nginx 的 Docker 映像檔並啟動容器

- 建立 Nginx 站台設定檔，並命名為 `default.conf`

    ```nginx
    server {
        listen 80;
        listen 443 ssl;
    
        ssl_certificate /etc/nginx/server.crt;
        ssl_certificate_key /etc/nginx/server.key;

        root /usr/share/nginx/html;
        index index.html;
    }
    ```

- 建立 Dockerfile

    ```dockerfile
    FROM nginx:alpine
    COPY default.conf /etc/nginx/conf.d/default.conf
    ```

- 建立 Docker 映像檔

    ```bash
    docker build -t nginxssl .
    ```

- 啟動容器

    ```bash
    docker run -it -d -p 80:80 -p 443:443 -v ${PWD}/server.key:/etc/nginx/server.key -v ${PWD}/server.crt:/etc/nginx/server.crt nginxssl
    ```

### 從本機開啟瀏覽器測試

以上步驟都是在 Linux (CentOS) 虛擬機器執行，如果要從本機 (Windows) 透過瀏覽器連線，注意以下幾點

- 記得將 Linux 虛擬機器 的 80 port 及 443 port 打開
- 在 `C:\Windows\System32\drivers\etc\hosts` 檔案加上 domain name，連線到 `example.com` 時會以這個 IP 優先

    ```txt
    192.168.2.100 example.com
    ```

- 打開瀏覽器連線到 `https://example.com`，會跳出你的連線不是私人連線，按下進階，繼續前往 example.com 網站即可
    ![image](https://user-images.githubusercontent.com/46283957/149251414-6aca8f2a-479e-47c1-b7c2-a813b2feb010.png)

### 相關連結

- [如何使用 OpenSSL 建立開發測試用途的自簽憑證 (Self-Signed Certificate)
](https://blog.miniasp.com/post/2019/02/25/Creating-Self-signed-Certificate-using-OpenSSL)
