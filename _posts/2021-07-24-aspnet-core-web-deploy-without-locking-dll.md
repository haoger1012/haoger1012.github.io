---
title: 發行 ASP.NET Core 網站到 IIS 且 dll 不被鎖定
categories:
- ASP.NET Core 
---

在使用 Web Deploy 發行 ASP.NET Core 到遠端 IIS 站台時，都會遇到 dll 檔被鎖定的問題，此時都會先去關閉 IIS 站台，等發行成功後再開啟，但最近找到了一個相當簡單的方式解決這個擾人的問題

### 解決方法

打開 `pubxml` 檔，在 `PropertyGroup` 裡面新增 `EnableMSDeployAppOffline` 並設定為 `true`，之後就能順利發行了

```xml
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <PropertyGroup>
        <EnableMSDeployAppOffline>true</EnableMSDeployAppOffline>
    </PropertyGroup>
</Project>
```

### 相關連結

- [Locked Files When Publishing .NET Core Apps to IIS with WebDeploy](https://weblog.west-wind.com/posts/2021/Jun/20/Locked-Files-When-Publishing-NET-Core-Apps-to-IIS-with-WebDeploy)
