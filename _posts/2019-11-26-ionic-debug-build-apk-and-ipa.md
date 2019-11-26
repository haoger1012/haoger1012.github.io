---
title: Ionic 偵錯、產生 apk 與 ipa
categories:
- Ionic 
---
### 建立 Ionic 專案
安裝 Ionic 與 Cordova
```bash
npm install -g ionic cordova
```
建立專案，選擇 Framework 與 Starter template
```bash
ionic start myApp
```
![](https://i.imgur.com/t4pIxj4.png)
啟動專案執行
```bash
ionic serve
```

### Ionic DevApp
在 [App Store](https://itunes.apple.com/us/app/ionic-devapp/id1233447133?ls=1&mt=8) 或 [Google Play](https://play.google.com/store/apps/details?id=io.ionic.devapp&hl=en) 下載 Ionic DevApp

在專案執行
```bash
ionic serve --devapp
```
將行動裝置與開發環境在同一個區域網路內即可使用

> Ionic DevApp 支援使用 [Cordova 套件](https://ionicframework.com/docs/appflow/devapp/#native-cordova-plugin-support)呼叫原生程式，但並非全部支援

### Android
#### 偵錯
在專案產生 Android 執行平台
```bash
ionic cordova prepare android
```
產生的檔案會在 `platforms/android`

使用 [Chocolatey](https://chocolatey.org/) 安裝 JDK、Gradle、Android Studio
```bash
choco install jdk8 gradle androidstudio -y
```
開啟 Android Studio 並開啟 `platform/android`，Android Studio 會檢查是否有未安裝的套件，直接安裝即可

使用 USB 連接電腦和行動裝置 (須開啟開發人員選項啟動 USB 偵錯)，若有偵測到裝置會出現在下拉選單

選擇模擬器或行動裝置，點選 Run 'app'

然後在 Google Chrome 網址列輸入 `chrome://inspect`，在 Devices 頁籤中找到執行的模擬器，在模擬器開啟的程式網址中點擊 inspect

> 啟用 live-reload 偵錯，執行 `ionic cordova run android -l`

#### 產生 apk
在專案產生 apk
```bash
ionic cordova build android
```
產生的 apk 會在 `platforms\android\app\build\outputs\apk\debug` 

然後將 apk 放入行動裝置並安裝即可

### iOS
#### 偵錯
在專案產生 iOS 執行平台
```bash
ionic cordova prepare ios
```
產生的檔案會在 `platforms/ios`，打開裡面的 `myApp.xcodeproj`，會開啟 Xcode

修改 config.xml 中 <widget> 標籤的 id 屬性與 myApp 中 Identity 的 Bundle Identifier，兩者須設定一致

![](https://i.imgur.com/e9YNgYU.png)
![](https://i.imgur.com/yv29jH8.png)

但 Bundle Identifier 並不是隨便取名，須到 [Apple Developer](https://developer.apple.com/) 自己的帳號下新增一個 Identifier 才能夠使用 
![](https://i.imgur.com/IiFODit.png)
![](https://i.imgur.com/llcBmkC.png)

在 myApp 中 Signing 的 Team 選擇自己的 Team
![](https://i.imgur.com/WK999Ly.png)

選擇模擬器或行動裝置後點選執行

![](https://i.imgur.com/MXU3eEE.png)

偵錯方式有兩種：
1. 在 Xcode 開啟除錯視窗 (command + shift + C)
2. 下載 [Safari Technology Preview](https://developer.apple.com/safari/technology-preview/)，下載完成後開啟點選 Develop 頁籤選擇要執行的模擬器或行動裝置，即可打開 Web Inspector

> 啟用 live-reload 偵錯，執行 `ionic cordova run ios -l --address=0.0.0.0`

#### 產生 ipa
在 `Product` > `Destination` 選擇 `Generic iOS Device`，然後在 `Product` 選擇 `Archive`
> 第一次執行發生了錯誤，把在 myApp 中 Signing 的 Automatically manage signing 取消勾選再勾選一次即可

在 `Window` 選擇 `Organizer`，點選剛剛的產生的 Archive，再點選旁邊的 Distribute App，選擇 Development，之後照著圖片中的步驟就會產生一個資料夾，裡面有 ipa
![](https://i.imgur.com/ypwnh8l.png)
![](https://i.imgur.com/gaa6VG5.png)

然後在 `Window` 選擇 `Devices and Simulators`，把 ipa 拖曳到行動裝置的 Installed Apps 即可進行安裝
#### OTA (over-the-air)
這個方式不用透過 App Store、TestFlight 等複雜的方式，就可以讓其他人安裝 ipa
1. 與剛剛的步驟大同小異，這次選擇 Ad Hoc
![](https://i.imgur.com/aa18REH.png)
2. Addtional Options 要勾選
![](https://i.imgur.com/AnXH7iZ.png)
3. 輸入要放在 server 的 ipa 與 image 路徑 (可先隨便填，之後都能改)
![](https://i.imgur.com/WjRYXEi.png)
4. 編輯 `manifest.plist`，修改 ipa 路徑 (如果上一步寫好，這步就可以省略)
    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
    <plist version="1.0">
    <dict>
        <key>items</key>
        <array>
            <dict>
                <key>assets</key>
                <array>
                    <dict>
                        <key>kind</key>
                        <string>software-package</string>
                        <key>url</key>
                        <string>https://www.example.com/myApp.ipa</string>
                        </dict>
                    <dict>
                        <key>kind</key>
                        <string>display-image</string>
                        <key>url</key>
                        <string>https://www.example.com/image.57x57.png</string>
                    </dict>
                    <dict>
                        <key>kind</key>
                        <string>full-size-image</string>
                        <key>url</key>
                        <string>https://www.example.com/image.512x512.png</string>
                    </dict>
                </array>
                <key>metadata</key>
                <dict>
                    <key>bundle-identifier</key>
                    <string>myApp</string>
                    <key>bundle-version</key>
                    <string>0.0.1</string>
                    <key>kind</key>
                    <string>software</string>
                    <key>title</key>
                    <string>myApp</string>
                </dict>
            </dict>
        </array>
    </dict>
    </plist>
    ```
5. 新增一個 `index.html`，修改 url 的 `manifest.plist` 路徑
    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="utf-8" />
        <title></title>
    </head>
    <body>
        <a href="itms-services://?action=download-manifest&url=https://www.example.com/ manifest.plist">My App</a>
    </body>
    </html>
    ```
6. 行動裝置透過 Safari 到 `index.html` 點選連結即可下載 ipa 進行安裝

> 透過 Ad Hoc 的方式，須到 [Apple Developer](https://developer.apple.com/) 自己的帳號下新增一個 Device 才能夠安裝下載的 ipa
![](https://i.imgur.com/QWkNAtb.png)
但如果有企業帳號，可透過 Enterprise 的方式讓所有人透過連結下載 ipa，毋須新增 Device

### 相關連結
- [Ionic - Cross-Platform Mobile App Development](https://ionicframework.com/)
- [I get conflicting provisioning settings error when I try to archive to submit an iOS app](https://stackoverflow.com/questions/40824727/i-get-conflicting-provisioning-settings-error-when-i-try-to-archive-to-submit-an)
- [iOS 如何利用 AdHoc 發佈 App](https://medium.com/@esther.tsai/ios-%E5%A6%82%E4%BD%95%E5%88%A9%E7%94%A8-adhoc-%E7%99%BC%E4%BD%88-app-648f1344b8a6)
- [Over The Air (OTA) iOS IPA File Distribution For Public?](https://stackoverflow.com/questions/26042508/over-the-air-ota-ios-ipa-file-distribution-for-public)
