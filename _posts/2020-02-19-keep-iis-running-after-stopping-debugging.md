---
title: Visual Studio 結束偵錯後持續開啟 IIS Express
categories:
- Visual Studio 
---
### 解決方法

Visual Studio 專案啟動偵錯時，IIS Express 會開啟，但每次結束偵錯時， IIS Express 就關閉了，若想要持續開啟狀態，對著專案右鍵點選屬性，會看到以下畫面
![image](https://user-images.githubusercontent.com/46283957/149251128-31e3840c-45d4-4c62-a264-5ca88c69d392.png)
接著在偵錯的頁籤中的裝載模型變更為外部處理序即可

### 相關連結

- [Can I keep an ASP.NET Core web site running after stopping debugging?](https://stackoverflow.com/questions/55251484/can-i-keep-an-asp-net-core-web-site-running-after-stopping-debugging)
