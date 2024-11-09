---
title: ASP.NET Core 依賴注入的 ServiceProviderOptions    
categories:
- ASP.NET Core
---

公司的某專案經常切換成其他非 Development 的環境開發，而這個專案啟動時會先連資料庫查詢資料，程式碼大致上如以下所示：

```csharp
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddScoped<TestService>();

var app = builder.Build();
var testService = app.Services.GetRequiredService<TestService>();
var data = await testService.QueryAsync();
```

這段程式碼部署到 Production 的環境後從未發生例外，但在本地開發時切換成 Development 的環境後就出現例外了，錯誤訊息如下：

```txt
System.InvalidOperationException: 'Cannot resolve scoped service 'TestService' from root provider.'
```

這時想起在 Singleton 物件注入 Scoped 物件時，這個 Scoped 物件本身就跟 Singleton 物件沒什麼不同，而這個 Scoped 物件在應用程式關閉時才會被釋放，這可能會造成一些問題

但奇怪的是為何在 Production 的環境不會報錯，在 Development 的環境才會報錯呢？

### 深入原始碼

後來在原始碼中尋找發生這個現象的原因，從 `WebApplication.CreateBuilder(args)` 這一行程式碼往下找，最後在 [HostingHostBuilderExtensions.cs](https://github.com/dotnet/runtime/blob/main/src/libraries/Microsoft.Extensions.Hosting/src/HostingHostBuilderExtensions.cs#L325-L333) 找到解答

```csharp
internal static ServiceProviderOptions CreateDefaultServiceProviderOptions(HostBuilderContext context)
{
    bool isDevelopment = context.HostingEnvironment.IsDevelopment();
    return new ServiceProviderOptions
    {
        ValidateScopes = isDevelopment,
        ValidateOnBuild = isDevelopment,
    };
}
```

從這段原始碼可以得知只有在 Development 的環境下，`ValidateScopes` 和 `ValidateOnBuild` 為 `true`，這在開發時期能避免這種隱藏的問題發生

而這兩個屬性會在 [ServiceProvider.cs](https://github.com/dotnet/runtime/blob/main/src/libraries/Microsoft.Extensions.DependencyInjection/src/ServiceProvider.cs#L68-L93) 的建構函式中進行 Scope 的檢查

```csharp
if (options.ValidateScopes)
{
    _callSiteValidator = new CallSiteValidator();
}

if (options.ValidateOnBuild)
{
    List<Exception>? exceptions = null;
    foreach (ServiceDescriptor serviceDescriptor in serviceDescriptors)
    {
        try
        {
            ValidateService(serviceDescriptor);
        }
        catch (Exception e)
        {
            exceptions ??= new List<Exception>();
            exceptions.Add(e);
        }
    }

    if (exceptions != null)
    {
        throw new AggregateException("Some services are not able to be constructed", exceptions.ToArray());
    }
}
```

### 解決方法

先使用 `CreateScope` 建立 Scope 後再去取得物件，避免從根容器取得物件

```csharp
var app = builder.Build();
using var scope = app.Services.CreateScope();
var testService = scope.ServiceProvider.GetRequiredService<TestService>();
```

### 相關連結

- [ASP.NET Core 依赖注入系统学习教程：针对服务注册的验证 - 姜承轩 - 博客园](https://www.cnblogs.com/green-jcx/p/16566740.html)
