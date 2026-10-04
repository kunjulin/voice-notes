# voice-notes

語音智慧筆記：錄音 → Gemini 多語言轉錄與結構化摘要。

## 暫存與備份
- 錄音中每 3 秒把音訊片段寫入本機 IndexedDB；頁面關閉或當機，下次開啟會自動從片段復原。
- 錄音結束先轉成 MP3（16 kHz 單聲道），再送 Gemini；MP3 轉檔失敗則保留原始檔。
- Gemini 忙碌（429 / 5xx / 網路錯誤）會自動重試（次數可在設定調整），仍失敗則留在「暫存紀錄」可手動重試。
- 超過約 14 MB 的音檔改走 Gemini Files API，避開 inline 20 MB 上限。
- 每筆紀錄可下載 MP3 與 Markdown（含裝置即時辨識的備援文字）。
- iOS 建議「加入主畫面」使用，避免 Safari 清除久未開啟網站的本機資料。

## OneDrive 備份（選用）
1. Entra ID（Azure Portal）→ 應用程式註冊 → 新增，平台選「單頁應用程式 (SPA)」，Redirect URI 填本頁網址（例如 `https://kunjulin.github.io/voice-notes/`）。
2. API 權限加入 Microsoft Graph 委派權限 `Files.ReadWrite`、`offline_access`。
3. 在 App 設定填入 Client ID、Tenant（個人帳號 `consumers`，組織帳號填 tenant ID，或 `common`）、資料夾，按「連結 OneDrive」。
4. 勾選自動上傳後，錄音完成會上傳 MP3 與 Markdown，分析完成再更新 Markdown。
