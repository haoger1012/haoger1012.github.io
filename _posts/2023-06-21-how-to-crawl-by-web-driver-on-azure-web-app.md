---
title: 如何在 Azure Web App 透過 Web Driver 爬蟲
categories:
- ASP.NET Core
- Microsoft Azure
---

最近需要在 Web API 專案透過 Web Driver 爬蟲，在本機可以順利執行爬蟲，但部署到 Azure Web App 後就失敗了

一開始是使用 [Playwright for .NET](https://playwright.dev/dotnet/) 去爬蟲

```csharp
[HttpGet("playwright")]
public async Task<IActionResult> Playwright()
{
    try
    {
        using var playwright = await Playwright.CreateAsync();
        await using var browser = await playwright.Chromium.LaunchAsync();
        var page = await browser.NewPageAsync();
        await page.GotoAsync("https://www.google.com");
        var content = await page.ContentAsync();

        return Ok(content);
    }
    catch (Exception ex)
    {
        return BadRequest(ex.ToString());
    }
}

```

但部署至 Azure Web App 後回傳以下錯誤訊息，坦白說根本看不出什麼東西，也查不到什麼解決方法

```txt
Microsoft.Playwright.PlaywrightException: spawn UNKNOWN
=========================== logs ===========================
<launching> ms-playwright\chromium-1067\chrome-win\chrome.exe --disable-field-trial-config --disable-background-networking --enable-features=NetworkService,NetworkServiceInProcess --disable-background-timer-throttling --disable-backgrounding-occluded-windows --disable-back-forward-cache --disable-breakpad --disable-client-side-phishing-detection --disable-component-extensions-with-background-pages --disable-component-update --no-default-browser-check --disable-default-apps --disable-dev-shm-usage --disable-extensions --disable-features=ImprovedCookieControls,LazyFrameLoading,GlobalMediaControls,DestroyProfileOnBrowserClose,MediaRouter,DialMediaRouteProvider,AcceptCHFrame,AutoExpandDetailsElement,CertificateTransparencyComponentUpdater,AvoidUnnecessaryBeforeUnloadCheckSync,Translate --allow-pre-commit-input --disable-hang-monitor --disable-ipc-flooding-protection --disable-popup-blocking --disable-prompt-on-repost --disable-renderer-backgrounding --force-color-profile=srgb --metrics-recording-only --no-first-run --enable-automation --password-store=basic --use-mock-keychain --no-service-autorun --export-tagged-pdf --headless --hide-scrollbars --mute-audio --blink-settings=primaryHoverType=2,availableHoverTypes=2,primaryPointerType=4,availablePointerTypes=4 --no-sandbox --user-data-dir=D:\local\Temp\playwright_chromiumdev_profile-nRoIW9 --remote-debugging-pipe --no-startup-window
============================================================
```

後來改成使用 [Puppeteer Sharp](https://www.puppeteersharp.com/) 看看

```csharp
[HttpGet("puppeteer")]
public async Task<IActionResult> Puppeteer()
{
    try
    {
        using var browserFetcher = new BrowserFetcher();
        await browserFetcher.DownloadAsync(BrowserFetcher.DefaultChromiumRevision);
        var browser = await Puppeteer.LaunchAsync(new LaunchOptions
        {
            Headless = true
        });
        var page = await browser.NewPageAsync();
        await page.GoToAsync("https://www.google.com");
        var content = await page.GetContentAsync();

        return Ok(content);
    }
    catch (Exception ex)
    {
        return BadRequest(ex.ToString());
    }
}

```

這次部署至 Azure Web App 後回傳以下錯誤訊息，看起來錯誤訊息是有比較清楚一點了

```txt
System.ComponentModel.Win32Exception (14001): An error occurred trying to start process 'D:\home\site\wwwroot\.local-chromium\Win64-1069273\chrome-win\chrome.exe' with working directory 'D:\home\site\wwwroot'. The application has failed to start because its side-by-side configuration is incorrect. Please see the application event log or use the command-line sxstrace.exe tool for more detail.
```

後來在 Puppeteer Sharp 的 GitHub Issue 底下的 [comment](https://github.com/hardkoded/puppeteer-sharp/issues/383#issuecomment-403492867) 找到，其實沒辦法在 Azure Web App 上跑 Chrome，但提供了一個解法

### 解決方法

藉由 [Browserless](https://www.browserless.io/) 這個免費的服務，註冊後取得 API Token 就可以使用了

後端透過 Web Socket 連到遠端的瀏覽器來爬蟲，就不需要在 Azure Web App 跑瀏覽器了，於是將程式碼改寫一下

- Playwright for .NET

  ```csharp
  [HttpGet("playwright")]
  public async Task<IActionResult> Playwright()
  {
      try
      {
          using var playwright = await Playwright.CreateAsync();
          await using var browser = await playwright.Chromium.ConnectOverCDPAsync("wss://chrome.browserless.io?token=YOUR-API-TOKEN");
          var page = await browser.NewPageAsync();
          await page.GotoAsync("https://www.google.com");
          var content = await page.ContentAsync();
  
          return Ok(content);
      }
      catch (Exception ex)
      {
          return BadRequest(ex.ToString());
      }
  }
  ```
  
- Puppeteer Sharp

  ```csharp
  [HttpGet("puppeteer")]
  public async Task<IActionResult> Puppeteer()
  {
      try
      {
          var options = new ConnectOptions()
          {
              BrowserWSEndpoint = $"wss://chrome.browserless.io?token=YOUR-API-TOKEN"
          };
  
          var browser = await Puppeteer.ConnectAsync(options);
          var page = await browser.NewPageAsync();
          await page.GoToAsync("https://www.google.com");
          var content = await page.GetContentAsync();
  
          return Ok(content);
      }
      catch (Exception ex)
      {
          return BadRequest(ex.ToString());
      }
  }
  
  ```

### 相關連結

- [Support for Azure · Issue #383 · hardkoded/puppeteer-sharp](https://github.com/hardkoded/puppeteer-sharp/issues/383)
- [Using Chrome in Azure with Puppeteer Sharp and Browserless.io](http://www.hardkoded.com/blogs/azure-chrome-puppeteer-browserless)
