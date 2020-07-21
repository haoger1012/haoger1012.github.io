---
title: Azure Function 部署發生錯誤 Error File In Use
categories:
- Azure 
---
Azure Function 的專案在部屬時發生了錯誤，平常都沒事發生，今天 CD 時卻意外發生錯誤，後來連手動部屬也無法成功，於是到 Azure DevOps 上找發生問題的原因，錯誤訊息如下：
```bash
2020-07-21T02:36:28.4012798Z Error Code: ERROR_FILE_IN_USE
2020-07-21T02:36:28.4014649Z More Information: Web Deploy cannot modify the file 'Microsoft.Azure.WebJobs.Extensions.Storage.dll' on the destination because it is locked by an external process.  In order to allow the publish operation to succeed, you may need to either restart your application to release the lock, or use the AppOffline rule handler for .Net applications on your next publish attempt.  Learn more at: http://go.microsoft.com/fwlink/?LinkId=221672#ERROR_FILE_IN_USE.
2020-07-21T02:36:28.4018467Z Error count: 1.
2020-07-21T02:36:28.4821884Z ##[error]Process completed with exit code 1.
```
靠著錯誤訊息，找到了解決方法，到 App Service 的 Configuration 加上 `MSDEPLOY_RENAME_LOCKED_FILES = 1` 的設定再重新部屬即可
![](https://i.imgur.com/NYsS7Qc.png)

### 相關連結
- [Azure Function: Publish fails with message “Web Deploy cannot modify the file on the Destination because it is locked by an external process.”](https://stackoverflow.com/questions/48329974/azure-function-publish-fails-with-message-web-deploy-cannot-modify-the-file-on)
