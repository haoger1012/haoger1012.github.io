---
title: HttpClient BaseAddress 的地雷
categories:
- C# 
---
在使用 `HttpClient` 時去呼叫 API 時，通常都會設定 `BaseAddress`，但最近遇到了一個地雷，紀錄一下：

```csharp
private readonly IHttpClientFactory _clientFactory;

public MyService(IHttpClientFactory clientFactory)
{
    _clientFactory = clientFactory;
}

public async Task Get(int id)
{
    var client = _clientFactory.CreateClient();
    client.BaseAddress = new Uri("http://localhost/api");
    var response = await client.GetAsync($"user/{id}");
    // 以下省略
}
```

這時以為組出來的 URL 應該會是 `http://localhost/api/user/1`，但結果卻是 `http://localhost/user/1`

經過爬文，`BaseAddress` 需在最後加上 `/`，才能組出正確的 URL

```csharp
client.BaseAddress = new Uri("http://localhost/api/");
```

### 相關連結

- [Why is HttpClient BaseAddress not working?](https://stackoverflow.com/questions/23438416/why-is-httpclient-baseaddress-not-working)
- [HttpClient with BaseAddress](https://stackoverflow.com/questions/20609118/httpclient-with-baseaddress)
