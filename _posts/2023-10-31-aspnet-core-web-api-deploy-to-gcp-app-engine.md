---
title: 將 ASP.NET Core Web API 部署至 GCP App Engine
categories:
- ASP.NET Core
---

### 安裝 Google Cloud CLI

- 透過 PowerShell 安裝

    ```powershell
    (New-Object Net.WebClient).DownloadFile("https://dl.google.com/dl/cloudsdk/channels/rapid/GoogleCloudSDKInstaller.exe", "$env:Temp\GoogleCloudSDKInstaller.exe")

    & $env:Temp\GoogleCloudSDKInstaller.exe
    ```

- 透過 [Chocolatey](https://chocolatey.org/install) 安裝

    ```powershell
    choco install gcloudsdk -y
    ```

### 事前準備

- 建立一個版本為 .NET 6 Web API 專案

    ```sh
    dotnet new webapi -n Demo -f net6.0
    ```

- 在專案根目錄下建立 `app.yaml`

    ```yaml
    runtime: aspnetcore
    env: flex

    runtime_config:
      operating_system: "ubuntu22"
    ```

- 記得將 `app.yaml` 複製到輸出目錄
  
    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

    <PropertyGroup>
      <TargetFramework>net6.0</TargetFramework>
      <Nullable>enable</Nullable>
      <ImplicitUsings>enable</ImplicitUsings>
    </PropertyGroup>

    <ItemGroup>
      <None Update="app.yaml">
        <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
      </None>
    </ItemGroup>

    </Project>
    ```

### 初始化 gcloud CLI

```sh
gcloud init
```

過程中會要求登入、選擇或建立專案，最後出現驗證成功

### 部署

執行 `gcloud app deploy` (記得確認當前目錄下包含 `app.yaml`)

```sh
gcloud app deploy
```

### 相關連結

- [The .NET runtime](https://cloud.google.com/appengine/docs/flexible/dotnet/runtime)
- [Create a .NET app in the App Engine flexible environment](https://cloud.google.com/appengine/docs/flexible/dotnet/create-app)
