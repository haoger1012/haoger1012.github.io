---
title: 如何連接 Azure Web App 的 MySQL in App
categories:
- Microsoft Azure 
---

在啟用 Azure Web App 的 MySQL in App 功能後，它會提供一個環境變數讓你取得連線字串

![image](https://user-images.githubusercontent.com/46283957/169201309-c68927df-25f7-4e14-b744-876a56085f4a.png)

而環境變數 `MYSQLCONNSTR_localdb` 的內容是

```txt
Database=localdb;Data Source=127.0.0.1:*****;User Id=azure;Password=********
```

結果這連線字串根本無法連到資料庫！😠

### 解決方法

找了資料後發現連線字串應該長得像下面這種格式

```txt
Server=127.0.0.1;Port=*****;User=azure;Password=********;Database=localdb;
```

於是寫了一個方法去轉換連線字串

```csharp
string GetConnectionStringFromMySqlInApp()
{
    var connStr = Environment.GetEnvironmentVariable("MYSQLCONNSTR_localdb");

    var dict = connStr.Split(';')
        .Select(kvp => kvp.Split('='))
        .ToDictionary(kvp => kvp[0], kvp => kvp[1]);

    var dataSource = dict["Data Source"].Split(':');
    var server = dataSource[0];
    var port = dataSource[1];
    var user = dict["User Id"];
    var password = dict["Password"];
    var database = dict["Database"];

    return $"Server={server};Port={port};User={user};Password={password};Database={database}";
}
```

轉換格式後就能順利連到資料庫了！

### 相關連結

- [ASP.NET Core MVC and MySql in App](https://social.msdn.microsoft.com/Forums/en-US/7e577b74-bbc8-41ea-a5e4-075b0eaa8622/aspnet-core-mvc-and-mysql-in-app)
- [Socket error when connecting to In App MySQL in Azure App Service](https://stackoverflow.com/a/58020841)
