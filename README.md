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
1. 用個人 Microsoft 帳號登入 [Azure Portal](https://portal.azure.com/) → Microsoft Entra ID → 應用程式註冊 → 新增註冊。
   - **支援的帳戶類型**：選「任何組織目錄中的帳戶及個人 Microsoft 帳戶」或「僅限個人 Microsoft 帳戶」。選錯會出現 `unauthorized_client ... not enabled for consumers`。
   - 已建立的應用程式：資訊清單 (Manifest) 把 `signInAudience` 改成 `AzureADandPersonalMicrosoftAccount`，並把 `api.requestedAccessTokenVersion` 設為 `2`。
2. 驗證 → 新增平台 → **單頁應用程式 (SPA)**，Redirect URI 填 `https://kunjulin.github.io/voice-notes/`（結尾要有 `/`）。
3. API 權限 → Microsoft Graph → 委派：`Files.ReadWrite`、`offline_access`。
4. 複製「應用程式 (用戶端) 識別碼」貼到 App 設定，Tenant 留空（= consumers，個人帳號），按「連結 OneDrive」，再按「測試連線」確認。
5. 請用 **Safari** 開啟（不要在 team+、LINE 等 App 的內建瀏覽器中開啟），並加入主畫面。

Microsoft 規定網頁 App（SPA）的授權每 24 小時要重新連結一次；過期時錄音仍保留在本機，重新連結後按「☁️ 上傳」補傳。
