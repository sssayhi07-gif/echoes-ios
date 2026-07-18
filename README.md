# Echoes → iOS App 包裝專案(Capacitor)

這個資料夾已經把你 `ee77` repo 裡的 `index.html`(Echoes)包裝成一個 Capacitor iOS 專案骨架,
可以直接推上 GitHub、接 Codemagic 雲端編譯,**不需要 Mac、不需要付費 Apple Developer 帳號**,
先產出一個「未簽章」的 `.ipa`,再用 Sideloadly 或 AltStore 簽到你自己的 iPhone 上測試。

（本次先不含推播通知功能,單純測試「包成 App」的效果。之後要加通知,再回來加 plugin 即可。）

## 資料夾結構

```
echoes-capacitor/
├── package.json          # Capacitor 依賴
├── capacitor.config.json # App 名稱 / Bundle ID / webDir 設定
├── codemagic.yaml         # 雲端編譯設定(免 Mac)
├── www/
│   └── index.html         # 你的 Echoes 網頁(從 ee77 repo 複製過來)
└── ios/                    # Capacitor 自動產生的 Xcode 專案骨架
```

Bundle ID 目前設成 `com.echoes.app`,App 名稱是 `Echoes`,如果要改,
編輯 `capacitor.config.json` 之後執行 `npx cap sync ios`。

## 上傳到 GitHub

```bash
cd echoes-capacitor
git init
git add .
git commit -m "Capacitor iOS 包裝專案"
git remote add origin https://github.com/你的帳號/echoes-ios.git
git push -u origin main
```

> 建議把 `node_modules/` 加進 `.gitignore`,不用整包上傳,Codemagic 會自己 `npm install`。

## 用 Codemagic 雲端編譯(免 Mac)

1. 到 [codemagic.io](https://codemagic.io) 用 GitHub 帳號註冊、連接這個 repo
2. Codemagic 會偵測到根目錄的 `codemagic.yaml`,選擇 workflow `ios-unsigned-test`
3. 直接點 **Start new build**
4. 建置完成後,在 Artifacts 區下載 `Echoes-unsigned.ipa`

這個建置**完全不需要任何 Apple 帳號、不需要憑證**,因為輸出的是「未簽章」的 App,
簽章這一步交給下一階段的 Sideloadly / AltStore 處理。

## 用 Sideloadly 裝到自己的 iPhone

1. 電腦(Windows 或 Mac 皆可)安裝 [Sideloadly](https://sideloadly.io/)
2. iPhone 用 USB 接電腦
3. 把 `Echoes-unsigned.ipa` 拖進 Sideloadly
4. 輸入你的免費 Apple ID 帳號密碼(這組帳號**不用**是付費開發者)
5. 按下開始,Sideloadly 會自動簽章並安裝到手機
6. 手機上「設定 → 一般 → VPN 與裝置管理」信任這個開發者憑證,才能打開 App

⚠️ 免費 Apple ID 簽的 App,**7 天後簽章會過期**,屆時 App 打不開,
重新用 Sideloadly 裝一次(或改用 AltStore,讓它自動續簽)即可。

## 之後要加推播通知

- 如果只是「App 自己本機排程提醒」(不用伺服器主動推),免費帳號可以做,用
  `@capacitor/local-notifications`
- 如果要「伺服器主動推播到手機」,APNs 遠端推播**一定要付費 Apple Developer 帳號($99/年)**,
  免費帳號在 Xcode 裡連 Push Notifications 這個選項都選不到,這是 Apple 的帳號層級限制,
  跟用什麼打包工具無關
