---
title: 連線字串的密碼消失了！？Persist Security Info 的用途
categories:
- .NET Core
- SQL Server
---

某天使用 LINQPad 寫程式，裡面混用了 LINQ to SQL 以及 Dapper，我認為很簡單的程式，卻莫名其妙出現 `SqlException`

如下圖所示，在第三次執行查詢時出現錯誤訊息 `Login failed for user 'sa'.`

![image](https://user-images.githubusercontent.com/46283957/218248109-99469b85-3bb6-4138-af62-8fe9600de5c3.png)

不都是同一個連線字串嗎？怎麼突然顯示無法登入呢？🤔

於是我把連線字串顯示出來

![image](https://user-images.githubusercontent.com/46283957/218248495-654f39e6-ead2-479a-84be-6f96e33160ce.png)

連線字串的密碼消失了！？

### 解決方法

為何會出現這樣的情形呢？我節錄了保哥的文章內容
> 在預設不加上 `Persist Security Info` 的情況下，預設為 `False`，當程式需要進行資料庫連線時，此時會將「敏感資訊」例如：密碼 (Password) 等資訊暫存在連線物件中 (記憶體裡)，當連線建立成功之後，就會立即將「敏感資訊」清除，這能確保記憶體中的「敏感資訊」會立即清除，降低資訊揭露 (Information Leakage) 的風險

所以在連線字串後加上 `Persist Security Info=True` 即可，如此一來，密碼就正常顯示了

![image](https://user-images.githubusercontent.com/46283957/218248681-c4971a70-8633-4994-b8be-ab4c8d04b905.png)

![image](https://user-images.githubusercontent.com/46283957/218248761-f98d21fe-f8a7-4846-bf7b-d68cf515516b.png)

但官方文件不建議將此參數設定為 `True`，非必要還是不要開啟 🔥

### 相關連結

- [Passing connection string from linqpad to custom library causes login failure](https://stackoverflow.com/questions/33969368/passing-connection-string-from-linqpad-to-custom-library-causes-login-failure)
- [Protecting Connection Information](https://learn.microsoft.com/en-us/dotnet/framework/data/adonet/protecting-connection-information)
- [講解 SQL 連線字串中關於 Persist Security Info 參數的用途](https://blog.miniasp.com/post/2009/07/17/SQL-Connection-String-Persist-Security-Info-Explained)
