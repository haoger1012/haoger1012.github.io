---
title: 在 SSMS 顯示欄位完整內容
categories:
- SQL Server 
---

從 SSMS 複製查詢結果是 `varchar(max)` 或 `nvarchar(max)` 的欄位時，由於字串內容太長，所以複製出來的字串都會被截斷，非常煩人，本篇文章記錄一下解決方法

### 解決方法

有兩種方法：

- 在 **Tools > Options** 裡面的 **Query Results > SQL Server > Results to Grid** 可以調整字數的限制
    ![image](https://user-images.githubusercontent.com/46283957/193963738-190ba2dd-361d-4eaf-bb85-b3e54a95ab74.png)

- 將上一種方法的 `XML data` 改為 `Unlimited`，將資料轉成 XML 的話，就沒有字數的限制了，而且查詢的結果會變成一個可以按的連結，按下去會另開一個新頁籤顯示完整資料

    ```sql
    SELECT CAST('<![CDATA[' + [YourColumn] + ']]>' AS XML) FROM [YourTable];
    ```

### 相關連結

- [How do I view the full content of a text or varchar(MAX) column in SQL Server 2008 Management Studio? - Stack Overflow](https://stackoverflow.com/questions/2759721/how-do-i-view-the-full-content-of-a-text-or-varcharmax-column-in-sql-server-20)
