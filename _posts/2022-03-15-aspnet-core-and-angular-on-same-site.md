---
title: ASP.NET Core Web API 整合 Angular 至同站台
categories:
- ASP.NET Core
- Angular
---

以下是我覺得將 ASP.NET Core Web API 整合 Angular 至同站台比較簡單的方式

### 事前準備

- 建立 Angular 專案 (版本 Angular 13)

  ```bash
  ng new demo1 --routing --style=css
  cd demo1
  ```

  - 建立 `demo` component

    ```bash
    ng generate component demo
    ```

  - 在 `app.module.ts` 匯入 `HttpClientModule`

    ```typescript
    @NgModule({
      declarations: [
        AppComponent,
        DemoComponent,
      ],
      imports: [
        BrowserModule,
        HttpClientModule, // 匯入 HttpClientModule
        AppRoutingModule
      ],
      providers: []
      bootstrap: [ AppComponent ]
    })
    export class AppModule { }
    ```

  - 在 `demo.component.ts` 注入 `HttpClient`

    ```typescript
    constructor(private http: HttpClient) { }
    ```

  - 在 `demo.component.ts` 呼叫 ASP.NET Core Web API 專案的 API

    ```typescript
    ngOnInit(): void {
      this.http.get('/weatherForecast')
        .subscribe(data => console.log(data))
    }
    ```

  - 在 `app-routing.module.ts` 加入 `demo` 路由

    ```typescript
    const routes: Routes = [
      {
        path: 'demo',
        component: DemoComponent
      }
    ];
    ```

- 建置 Angular 專案

  ```bash
  ng build --prod
  ```

### 整合至 ASP.NET Core Web API 專案

- 建立 ASP.NET Core Web API 專案 (版本 .NET 6)

  ```bash
  dotnet new webapi -n Demo
  cd Demo
  ```

- 建立 `wwwroot` 資料夾

  ```bash
  mkdir wwwroot
  ```

- 將 Angular 建置出的 `dist` 裡的 demo 資料夾的內容複製至 `wwwroot`

- 修改 `Program.cs`

  ```csharp
  var app = builder.Build();

  // Configure the HTTP request pipeline.
  if (app.Environment.IsDevelopment())
  {
      app.UseSwagger();
      app.UseSwaggerUI();
  }
  
  app.UseHttpsRedirection();
   
  // 讀取靜態檔案
  app.UseDefaultFiles();
  app.UseStaticFiles();

  app.UseAuthorization();

  app.MapControllers();
  // 若找不到 API，則導到 /index.html
  app.MapFallbackToFile("/index.html");

  app.Run();
  ```

- 執行程式並切到 `/demo` 路由，在 Console 就能看到順利呼叫 API
  
  ```bash
  dotnet run
  ```

  ![image](https://user-images.githubusercontent.com/46283957/158327527-900bf9dc-5ed8-42df-945c-a600de479a3e.png)
