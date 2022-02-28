---
title: Azure DevOps 設定 Angular 的 CI/CD Pipeline
categories:
- Azure DevOps 
---
### 事前準備

- 在 Azure DevOps 建立一個 Project
- 建立 Angular 專案
  
    ```bash
    ng new demo1 --routing --style=css
    ```

- 在 Azure 建立一個 Web App

    ![image](https://user-images.githubusercontent.com/46283957/155937465-66b427c4-f062-49bf-91c4-ee6f8cc6cef3.png)

### 建立 CI Pipeline

- 選擇程式碼來源 Azure Repos Git

    ![image](https://user-images.githubusercontent.com/46283957/155938527-6c435cc0-e2a9-4c0c-9bbb-dfe4aeb4052e.png)

- 選擇 Empty template

    ![image](https://user-images.githubusercontent.com/46283957/155938929-051a92ac-ebf7-4466-a4a7-cda5883fa3c2.png)

- 在 Tasks 頁籤的 Agent Job 新增 tasks
  - 新增 npm 的 Task (跑 npm install)

    ![image](https://user-images.githubusercontent.com/46283957/155939960-d30f870f-d35c-4cc8-8ea5-5aba7b2e72c2.png)
    ![image](https://user-images.githubusercontent.com/46283957/155940834-935c022a-c5d7-49c7-9a94-23f255b9428d.png)

  - 新增 npm 的 Task (build 靜態檔案)

    ![image](https://user-images.githubusercontent.com/46283957/155940947-14f871de-79e7-4bd0-916b-bf28f3217050.png)

  - 新增 Publish build artifacts 的 Task (Path to publish 要填上一步的 output path)

    ![image](https://user-images.githubusercontent.com/46283957/155941206-835d6025-3dfc-47e8-b7e7-5e6c9d1ae6c4.png)
    ![image](https://user-images.githubusercontent.com/46283957/155941550-ccd2adea-b3a0-4305-835b-a84b70ac372f.png)

- 在 Trigger 頁籤啟用 CI

    ![image](https://user-images.githubusercontent.com/46283957/155955192-7183d2d2-199c-4dad-9089-a7307bb66f82.png)

- 建完後按儲存

    ![image](https://user-images.githubusercontent.com/46283957/155941930-5b70e94d-fd72-4ed9-9808-e6faf658bbf4.png)

### 建立 Release Pipeline

- 選擇 Azure App Service deployment 的 template

    ![image](https://user-images.githubusercontent.com/46283957/155942988-3cd46f10-7fea-4bb6-86af-4ad485a44328.png)

- 新增 Artifacts (Source 選剛剛建立好的 CI Pipeline)

    ![image](https://user-images.githubusercontent.com/46283957/155943422-b33dff25-a924-444f-939c-91b7cc2de70e.png)

- 啟用 CD trigger

    ![image](https://user-images.githubusercontent.com/46283957/155943819-d001f1ec-04c5-447d-b67a-21ac0c3b5aff.png)

- 到 Stage 1 修改 task
  - 選擇在 Azure 事先準備的 Web App

    ![image](https://user-images.githubusercontent.com/46283957/155945079-fcb3268b-c32b-4f07-aee9-d7b463efc7bc.png)

  - 修改 Deploy Azure App Service 的 Task (Deployment method 要選 **Web Deploy**)

    ![image](https://user-images.githubusercontent.com/46283957/155957363-6835cc4a-7977-4059-8447-9e1af814a9b8.png)

- 改好後按儲存

### 上傳 web.config 至 Web App

如果前面的步驟都順利完成後，將 Angular 的程式碼至 Azure DevOps 的 Repo，跑完 CI/CD 後，開啟網頁會發現在切換路由時，網頁會出現 `The resource you are looking for has been removed, had its name changed, or is temporarily unavailable.` 的錯誤，此時需將 web.config 透過 FTP 上傳至與應用程式相同資料夾

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>  
    <rewrite>
      <rules>
        <rule name="Angular Routes" stopProcessing="true">
          <match url=".*" />
          <conditions logicalGrouping="MatchAll">
            <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="true" />
            <add input="{REQUEST_FILENAME}" matchType="IsDirectory" negate="true" />
          </conditions>
          <action type="Rewrite" url="/index.html" />
        </rule>
      </rules>
    </rewrite>
  </system.webServer>
</configuration>
```

### 相關連結

- [Deployment - Server configuration - Routed apps must fallback to index.html](https://angular.io/guide/deployment#fallback-configuration-examples)
