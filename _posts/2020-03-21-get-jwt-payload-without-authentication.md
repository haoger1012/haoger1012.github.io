---
title: 不透過認證取得 JWT payload
categories:
- ASP.NET Core 
---
有個需求需取得 JWT 的 payload，但這個 token 是其他系統產出的，意味著我們無法對 JWT 做認證 (Authentication)，沒辦法從 `HttpContext.User` 取得資訊

### 解決方法

將 `IHttpContextAccessor` 注入後，依下方的程式碼 decode，取得 token 內的 payload

```csharp
public readonly Info info = new Info();

public MyService(IHttpContextAccessor accessor)
{
    // 取得 token
    string token = accessor.HttpContext.Request.Headers["Authorization"].ToString();
    token = token.Replace("Bearer ", string.Empty);
    
    if (!string.IsNullOrWhiteSpace(token))
    {
        // 取得 payload
        var handler = new JwtSecurityTokenHandler();
        var jwtToken = handler.ReadToken(token) as JwtSecurityToken;
        var payload = JsonSerializer.Serialize(jwtToken.Claims.ToDictionary(k => k.Type, v => v.Value));
        
        // 轉成你要的類別
        Info = JsonSerializer.Deserialize<Info>(payload, new JsonSerializerOptions
        {
            PropertyNamingPolicy = JsonNamingPolicy.CamelCase,
        });
    }
}
```

### 相關連結

- [How to decode JWT Token?](https://stackoverflow.com/questions/38340078/how-to-decode-jwt-token)
