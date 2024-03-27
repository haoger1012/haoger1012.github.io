---
title: 透過 API 觸發 Azure WebJobs
categories:
- Microsoft Azure 
---

最近碰到需要透過手動觸發一個長時間執行作業的需求，於是我使用當前想到最方便的方式去實作，也就是 Azure Web App 中的 WebJobs

### 部署

當主控台應用程式或背景工作服務部署至 Azure Web App 後，在 WebJobs 頁籤可以看到剛部署好的 WebJobs

![image](https://github.com/haoger1012/haoger1012.github.io/assets/46283957/585978ec-8f8c-4f7d-b731-f025628d4d2b)

其實部署後的檔案和本身站台的 Web App 是放在一起的，意味著其實可以透過同一個 CI/CD Pipeline 去同時部屬 Web App 和 WebJobs，以下是 WebJobs 部署後的檔案路徑

```txt
d:\
  |-- home
      |-- site
          |-- wwwroot
              |-- App_Data
                  |-- jobs
                      |-- triggered
                      |   |-- job1
                      |   |   |-- run.bat
                      |   |   |-- other files
                      |   |-- job2
                      |       |-- run.bat
                      |       |-- other files
                      |-- continuous
                          |-- job3
                          |   |-- run.bat
                          |   |-- other files
                          |-- job4
                              |-- run.bat
                              |-- other files
```

### 手動觸發 WebJobs

這次主要是使用 **triggered** 方式的 WebJobs，想透過呼叫 API 去觸發執行作業，以下是使用 `HttpClient` 呼叫的範例

```csharp
var client = new HttpClient();

client.BaseAddress = new Uri("https://{yoursite}.scm.azurewebsites.net/");

var username = "YOUR_USERNAME";
var password = "YOUR_PASSWORD";

var credential = Convert.ToBase64String(Encoding.UTF8.GetBytes($"{username}:{password}"));
client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", credential);

var request = new HttpRequestMessage(HttpMethod.Post, "api/triggeredwebjobs/{job name}/run");
var response = await client.SendAsync(request);
```

上面程式碼的 `YOUR_USERNAME` 和 `YOUR_PASSWORD` 可以在下圖所示的位置取得

![image](https://github.com/haoger1012/haoger1012.github.io/assets/46283957/9715300a-a29f-45b5-a532-086f878793cb)

> 如果 WebJobs 還在執行中，不能重複觸發，會回傳狀態碼 409 `Cannot start a new run since job is already running.`

### 中斷 WebJobs

- 由於我的 WebJobs 執行時間很長，發現後來狀態會被自動轉成 `Aborted`，可以將環境變數 `WEBJOBS_IDLE_TIMEOUT` 及 `SCM_COMMAND_IDLE_TIMEOUT` 設定大一點的數值避免作業中斷，並將 Always On 開啟
- 由於我的 WebJobs 執行時間很長，想要把作業強制中斷，發現沒有可以中斷的地方，即便把 WebJobs 刪除也沒辦法，此時可開啟 ProcessExplorer (`https://{yoursite}.scm.azurewebsites.net/ProcessExplorer/`)，針對執行中的 WebJobs 按右鍵 kill 掉

### 相關連結

- [Run background tasks with WebJobs - Azure App Service](https://learn.microsoft.com/en-us/azure/app-service/webjobs-create)
- [WebJobs · projectkudu/kudu Wiki](https://github.com/projectkudu/kudu/wiki/WebJobs)
- [WebJobs API · projectkudu/kudu Wiki](https://github.com/projectkudu/kudu/wiki/WebJobs-API)
- [Azure WebJob with IDLE_TIMEOUT abort](https://developers.de/2018/04/16/azure-webjob-fails-with/)
- [How to stop/cancel a running Azure webjob? - Stack Overflow](https://stackoverflow.com/a/33989962)
