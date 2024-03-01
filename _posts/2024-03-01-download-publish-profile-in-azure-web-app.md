---
title: 在 Azure Web App 下載發行設定檔
categories:
- Microsoft Azure 
---

以前為了執行簡易的測試，會下載發行設定檔將程式部屬至 Web App，但這次要下載時發現被阻擋，出現 `Basic authentication is disabled.` 的訊息

![image](https://github.com/haoger1012/haoger1012.github.io/assets/46283957/09a15679-49d6-4a2f-a236-71e73448429e)

查看官方文件後得知需到 [Configuration] 開啟 **Basic Auth Publishing Credentials** (文章的日期是 2024/1/25，應該是近期更新的)

我實際的畫面跟官方文件不太一樣，但是大同小異，到 [Configuration] -> [General settings]，並開啟 **SCM Basic Auth Publishing Credentials** 或 **FTP Basic Auth Publishing Credentials** 任一項即可下載發行設定檔

![image](https://github.com/haoger1012/haoger1012.github.io/assets/46283957/08f28a21-3638-47b6-a7ef-45ca04ff4932)

### 相關連結

- [Get a publish profile from Azure App Service - Visual Studio (Windows)](https://learn.microsoft.com/en-us/visualstudio/azure/how-to-get-publish-profile-from-azure-app-service)
- [Disable basic authentication for deployment - Azure App Service](https://learn.microsoft.com/en-us/azure/app-service/configure-basic-auth-disable)
