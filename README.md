# llm_practices


## Local LLM

### 第一步：建立工作資料夾

在你的電腦（建議 D 槽）建立一個資料夾，命名為 `AI_Project`。

---

### 第二步：下載核心元件 (3 個檔案)

請將以下檔案全部下載並放入 `AI_Project` 資料夾中：

1. **llama.cpp 主程式 (b4512 CUDA 版)**
* [點我下載：llama-b4512-bin-win-cuda-cu12.4-x64.zip](https://www.google.com/search?q=https://github.com/ggml-org/llama.cpp/releases/download/b4512/llama-b4512-bin-win-cuda-cu12.4-x64.zip)
* **動作：** 解壓縮，將裡面所有的 `.exe` 檔案移到 `AI_Project` 根目錄。


2. **GPU 支援庫 (DLL)**
* [點我下載：cudart-llama-bin-win-cuda-12.4-x64.zip](https://www.google.com/search?q=https://github.com/ggml-org/llama.cpp/releases/download/b4512/cudart-llama-bin-win-cuda-12.4-x64.zip)
* **動作：** 解壓縮，將裡面所有的 `.dll` 檔案移到 `AI_Project` 根目錄（與 `.exe` 放在一起）。


3. **Phi-4-mini-Reasoning 模型檔**
* [點我下載：Phi-4-mini-reasoning-Q4_K_M.gguf](https://www.google.com/search?q=https://huggingface.co/unsloth/Phi-4-mini-reasoning-GGUF/resolve/main/Phi-4-mini-reasoning-Q4_K_M.gguf)
* **動作：** 直接放入 `AI_Project` 資料夾。



---

### 第三步：製作伺服器啟動腳本

這能讓模型在後台執行，供 AnythingLLM 連接。

1. 在資料夾內按右鍵 -> **新增文字文件**，命名為 `run_server.bat` (確認副檔名是 .bat)。
2. 右鍵點擊該檔案選擇 **編輯**，貼入以下代碼並存檔：

```batch
@echo off
CHCP 65001 > nul
TITLE Phi-4-mini Reasoning Server

:: 針對 4GB 顯存優化
:: -ctk q8_0 : KV 緩存量化，大幅節省顯存
:: -c 8192   : 設定 8192 tokens 上下文
llama-server.exe ^
  -m Phi-4-mini-reasoning-Q4_K_M.gguf ^
  -ngl 99 ^
  -fa ^
  -ctk q8_0 ^
  -ctv q8_0 ^
  -c 8192 ^
  --host 0.0.0.0 ^
  --port 8080

pause

```

---

### 第四步：安裝與設定 AnythingLLM (前端 UI)

1. **下載安裝：** 前往 [AnythingLLM 官網](https://useanything.com/download) 下載 Windows 版並完成安裝。
2. **串接後端：**
* 執行剛才做的 `run_server.bat`，看到 `HTTP server listening` 後不要關閉。
* 打開 AnythingLLM，進入左下角 **Settings (齒輪)**。
* 點擊 **LLM Preference**：
* **LLM Provider:** 選擇 `Generic OpenAI`。
* **Base URL:** 輸入 `http://127.0.0.1:8080/v1`。
* **API Key:** 隨便輸入任何字（例如 `123`）。
* **Model Name:** 輸入 `phi-4-mini`。
* **Token limit:** 輸入 `8192`。


* 點擊 **Save 儲存**。



---

### 第五步：開始使用 (長文重構與 RAG)

1. **建立工作區：** 在 AnythingLLM 建立一個名為 `Code_Refactor` 的 Workspace。
2. **上傳長文/程式碼：**
* 點擊 Workspace 裡的「上傳」按鈕。
* 丟入你想重構的程式碼檔案或 PDF 資料。
* 點擊 **Save and Embed**。


3. **對話：**
* 你現在可以像用 ChatGPT 一樣開始對話。
* **重構範例：** 「請分析我剛剛上傳的 `main.py`，找出效能瓶頸並重構它。」
* 系統會自動從檔案中抓取重點，並呼叫後台的 Phi-4-mini 進行推理回答。



---

### ⚠️ 給你的核心提醒 (針對 GTX 1650)

* **顯存保護：** 執行時請關閉 Chrome/Edge 瀏覽器。
* **推理過程：** Phi-4-mini-Reasoning 會先「思考」，在視窗中可能會看到思考邏輯，這代表它正在精確分析你的代碼，請耐心等待。
* **萬一閃退：** 如果執行 `run_server.bat` 時閃退，請將腳本中的 `-c 8192` 改小一點（如 `6144`）。
