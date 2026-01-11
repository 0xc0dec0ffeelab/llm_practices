# llm_practices


## Local LLM

### 第一步：下載必要檔案

請將以下三個檔案下載並解壓縮到同一個資料夾（例如 `C:\Phi4`）。

1. **llama.cpp 核心程式** (版本 b4512):
* [下載點：llama-b4512-bin-win-cuda-cu12.4-x64.zip](https://www.google.com/search?q=https://github.com/ggml-org/llama.cpp/releases/download/b4512/llama-b4512-bin-win-cuda-cu12.4-x64.zip)


2. **GPU 加速支援庫** (必須下載，否則無法啟動 GPU):
* [下載點：cudart-llama-bin-win-cuda-12.4-x64.zip](https://www.google.com/search?q=https://github.com/ggml-org/llama.cpp/releases/download/b4512/cudart-llama-bin-win-cuda-12.4-x64.zip)
* *下載後將裡面的 `.dll` 檔案解壓到與 `llama-cli.exe` 同資料夾。*


3. **Phi-4-mini 模型檔案** (Q4_K_M 版本):
* [下載點：Phi-4-mini-instruct-Q4_K_M.gguf](https://www.google.com/search?q=https://huggingface.co/unsloth/Phi-4-mini-instruct-GGUF/resolve/main/Phi-4-mini-instruct-Q4_K_M.gguf)



---

### 第二步：建立啟動腳本 (純文字模式)

在你的資料夾內，按右鍵「新增文字文件」，更名為 **`start_ai.bat`** (請確保副檔名是 `.bat`)。按右鍵編輯並貼入以下內容：

```batch
@echo off
CHCP 65001 > nul
TITLE Phi-4-mini (GTX 1650 Optimized)

:: 核心設定說明：
:: -ngl 99 : 強制模型全部進入 GPU 顯存
:: -fa     : 開啟 Flash Attention (1650 必開，省顯存且加速)
:: -c 4096 : 限制對話記憶，防止 4GB 顯存溢出
:: -i -ins : 開啟交互指令模式 (類似聊天視窗)

llama-cli.exe ^
  -m Phi-4-mini-instruct-Q4_K_M.gguf ^
  -ngl 99 ^
  -fa ^
  -c 4096 ^
  -i -ins ^
  --color ^
  --temp 0.7 ^
  -p "<|system|>你是一個專業的繁體中文 AI 助手，請用簡潔精準的繁體中文回答。<|end|>"

pause

```

---

### 第三步：如何操作與驗證

1. **啟動：** 雙擊 `start_ai.bat`。
2. **確認 GPU 運作：** 看到加載文字中出現 `CUDA0: NVIDIA GeForce GTX 1650` 且 `llm_load_tensors: offloaded 33/33 layers to GPU`，代表已完全啟動加速。
3. **對話：**
* 看到 `>` 符號後，直接打字並按 **Enter**。
* 如果要換行，請在行末打一個 `\` 再按 Enter。
* 想停止 AI 說話？按 **Ctrl + C**。



---

### 💡 效能提示 (針對 1650)

* **不要開 Chrome/Edge：** 瀏覽器會吃掉約 500MB~1GB 的顯存，這會直接導致 AI 變慢或閃退。
* **顯存觀察：** 運行中你可以同時打開「工作管理員」的「效能」頁面，查看 **專用 GPU 記憶體**，如果接近 3.8GB/4.0GB 是正常的，但如果超過 4GB 速度就會驟降。

