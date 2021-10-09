---
title: 使用 C# 建立捷徑
categories:
- C# 
---

本篇文章示範如何使用 C# 建立捷徑

- 透過 Visual Studio 新增 COM 參考，勾選 `Windows Script Host Object Model`
![image](https://user-images.githubusercontent.com/46283957/136642032-c1701f97-b925-4262-bee5-56ac005e4abb.png)

- `using IWshRuntimeLibrary` 後建立捷徑  

    ```csharp
    string pathLink = "C:\Users\Haoger\Desktop\test.lnk";
    string targetPath = "D:\Tests";

    IWshShell wshShell = new WshShellClass();
    IWshShortcut shortcut = (IWshShortcut)wshShell.CreateShortcut(pathLink);
    shortcut.TargetPath = targetPath.Replace("/", "\\");
    shortcut.Description = "It is a shortcut";
    shortcut.Save();
    ```

  - 寫完了之後發現 compile 不會過，會出現 `無法內嵌 Interop 類型 'WshShellClass'。請改用適當的介面。` 的錯誤訊息，這時將 IWshRuntimeLibrary COM 參考屬性中的內嵌 Interop 類型改成「否」即可
    ![image](https://user-images.githubusercontent.com/46283957/136642651-10c161e0-b6ac-459f-a313-0663c021dce2.png)
  > 特別注意 TargetPath 必須存在且不能 / 與 \ 混和，捷徑雖然能建立，但是按了沒效果

- 執行結果
  
    ![image](https://user-images.githubusercontent.com/46283957/136642528-929f8e54-2a38-4e8d-b549-bafc22f7b356.png)

### 相關連結

- [Create shortcut programmatically in C#](https://www.fluxbytes.com/csharp/create-shortcut-programmatically-in-c/)
- [無法內嵌 interop 型別 請改用適當的介面](http://gdlion.blogspot.com/2013/07/interop.html)
