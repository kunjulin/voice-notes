# voice-notes

語音智慧筆記：錄音 → Gemini 多語言轉錄與結構化摘要。

## 暫存與備份
- 錄音中每 3 秒把音訊片段寫入本機 IndexedDB；頁面關閉或當機，下次開啟會自動從片段復原。
- 錄音結束先轉成 MP3（16 kHz 單聲道），再送 Gemini；MP3 轉檔失敗則保留原始檔。
- Gemini 忙碌（429 / 5xx / 網路錯誤）會自動重試（次數可在設定調整），仍失敗則留在「暫存紀錄」可手動重試。
- 超過約 14 MB 的音檔改走 Gemini Files API，避開 inline 20 MB 上限。
- 每筆紀錄可下載 MP3 與 Markdown（含裝置即時辨識的備援文字）。
- iOS 建議「加入主畫面」使用，避免 Safari 清除久未開啟網站的本機資料。

## Google Drive 備份（選用）
1. [Google Cloud Console](https://console.cloud.google.com/) 建立專案 → 「API 和服務」啟用 **Google Drive API**。
2. 「OAuth 同意畫面」：使用者類型選「外部」，測試使用者加入自己的 Gmail（維持「測試中」即可）。
3. 「憑證」→ 建立 **OAuth 用戶端 ID**，類型選「網頁應用程式」，「已授權的 JavaScript 來源」填 `https://kunjulin.github.io`（不需要重新導向 URI）。
4. 在 App 設定填入用戶端 ID、資料夾名稱，按「連結 Google Drive」。
5. 勾選自動上傳後，錄音完成會上傳 MP3 與 Markdown，分析完成再更新同一個 Markdown 檔。

僅使用 `drive.file` 權限，App 只能存取自己建立的檔案。Google 存取權杖約 1 小時有效；過期後自動上傳會失敗並保留在本機，在暫存紀錄按「☁️ 上傳」即可重新授權並補傳。
