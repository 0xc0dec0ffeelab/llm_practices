# llm_practices


## Local LLM


### 第一步：建立工作資料夾

在你的 D 槽或 C 槽建立一個資料夾，命名為 `Phi4-Reasoning`。

### 第二步：下載 3 個核心元件

請將以下 3 個連結的檔案下載，並全部放入 `Phi4-Reasoning` 資料夾中。

1. **llama.cpp 主程式 (b4512 CUDA 版)**
* [點我下載：llama-b4512-bin-win-cuda-cu12.4-x64.zip](https://www.google.com/search?q=https://github.com/ggml-org/llama.cpp/releases/download/b4512/llama-b4512-bin-win-cuda-cu12.4-x64.zip)
* **動作：** 解壓縮後，將裡面所有的 `.exe` 檔案移到資料夾根目錄。


2. **GPU 核心支援庫 (DLL)**
* [點我下載：cudart-llama-bin-win-cuda-12.4-x64.zip](https://www.google.com/search?q=https://github.com/ggml-org/llama.cpp/releases/download/b4512/cudart-llama-bin-win-cuda-12.4-x64.zip)
* **動作：** 解壓縮後，將裡面的 `.dll` 檔案移到資料夾根目錄（這步沒做會無法使用 GPU）。


3. **Phi-4-mini-Reasoning 模型檔 (Q4_K_M 量化)**
* [點我下載：Phi-4-mini-reasoning-Q4_K_M.gguf](https://www.google.com/search?q=https://huggingface.co/unsloth/Phi-4-mini-reasoning-GGUF/resolve/main/Phi-4-mini-reasoning-Q4_K_M.gguf)
* **動作：** 直接放入資料夾，不要改名。



---

### 第三步：建立「長文重構優化」啟動檔

1. 在資料夾內按右鍵 -> **新增文字文件**。
2. 將檔案更名為 `start_reasoning.bat` (確認副檔名是 **.bat**)。
3. 右鍵點擊 `start_reasoning.bat` 選擇 **編輯**，貼入以下優化代碼：

```batch
@echo off
CHCP 65001 > nul
TITLE Phi-4-mini-Reasoning (GTX 1650 Max Logic)

:: 優化說明：
:: -ngl 99   : 模型全入 4GB 顯存
:: -fa       : 開啟 Flash Attention 加速
:: -ctk q8_0 : KV 緩存量化 (關鍵！讓 4GB 顯卡能記住更多長代碼)
:: -c 8192   : 擴展上下文到 8192，適合分析中大型 Function
:: --temp 0.1: 低隨機性，確保重構程式碼不亂寫

llama-cli.exe ^
  -m Phi-4-mini-reasoning-Q4_K_M.gguf ^
  -ngl 99 ^
  -fa ^
  -ctk q8_0 ^
  -ctv q8_0 ^
  -c 8192 ^
  -i -ins ^
  --color ^
  --temp 0.1 ^
  --chat-template phi4 ^
  -p "<|system|>你是一個具備深度推理能力的資深軟體工程師。在重構代碼前，你會先進行邏輯思考，找出最優解。請使用繁體中文與用戶交流。<|end|>"

pause

```

---

### 第四步：啟動與操作驗證

1. **關閉瀏覽器：** 為了確保顯存充足，請關閉 Chrome 或 Edge。
2. **執行：** 雙擊 `start_reasoning.bat`。
3. **檢查畫面：**
* 看到 `llm_load_tensors: offloaded 33/33 layers to GPU` 代表成功。
* 看到 `CUDA0: NVIDIA GeForce GTX 1650` 代表偵測到你的顯卡。


4. **開始對話：**
* **重構指令範例：** 「請幫我重構這段 Python 程式碼，優化內存佔用並增加可讀性：[貼上你的程式碼]」
* **觀察思考過程：** 你會看到它先輸出一段分析邏輯（Reasoning），最後才給出重構後的代碼。



---

### 💡 針對「長文/重構」的特別提醒：

* **如果閃退：** 這是因為 8192 上下文太吃顯存，請編輯 `.bat` 檔將 `-c 8192` 改為 `-c 6144`。
* **換行貼上：** 如果你的程式碼有很多行，建議先在記事本寫好，然後一次複製，在終端機視窗點擊 **右鍵** 即可貼上。
* **停止輸出：** 如果 AI 講太長，按一次 **Ctrl + C** 即可停止。
