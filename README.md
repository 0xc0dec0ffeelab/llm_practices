# AI Papers

# Local LLM

## 一、環境前提（先確認）

請確認你符合以下條件（你前面描述是 OK 的）：

* Windows 10 / 11（64 位元）
* NVIDIA GPU（例如 GTX 1650，4GB VRAM）
* 系統 RAM ≥ 16GB（32GB 更佳）
* **不用安裝 Python、不用 Docker**

---

## 二、建立工作資料夾

1. 打開檔案總管
2. 到 `D:\`（或任一你喜歡的磁碟）
3. 新增資料夾，命名為：

```
AI_Project
```

後面所有東西都放這裡。

---

## 三、下載核心元件（一定要 3 個）

### ① llama.cpp 主程式（CUDA 版）

* 下載：

  ```
  llama-b4512-bin-win-cuda-cu12.4-x64.zip
  ```
* 來源：ggml-org / llama.cpp 官方 release

**操作：**

1. 解壓縮
2. 把裡面所有 `.exe` 檔案
   👉 **全部複製到 `AI_Project` 根目錄**

---

### ② CUDA Runtime DLL（GPU 支援）

* 下載：

  ```
  cudart-llama-bin-win-cuda-12.4-x64.zip
  ```

**操作：**

1. 解壓縮
2. 把裡面所有 `.dll` 檔案
   👉 **全部複製到 `AI_Project` 根目錄**
3. 確認 `.exe` 和 `.dll` 在同一層

---

### ③ Qwen2.5-Coder-7B 模型檔（重點）

**模型名稱（請照這個）：**


[Qwen2.5-Coder-7B-Instruct-Q4_K_M.gguf](https://huggingface.co/unsloth/Qwen2.5-Coder-7B-Instruct-GGUF?utm_source=chatgpt.com&show_file_info=Qwen2.5-Coder-7B-Instruct-Q4_K_M.gguf)


**操作：**

1. 下載 `.gguf`
2. **直接放進 `AI_Project` 資料夾**
3. 不用解壓縮

---

### ✅ 此時資料夾結構應該長這樣

```
AI_Project
│
├─ llama-server.exe
├─ llama-cli.exe
├─ (其他 .exe)
├─ *.dll
├─ Qwen2.5-Coder-7B-Instruct-Q4_K_M.gguf
```

---

## 四、建立伺服器啟動腳本（最重要）

### 1️⃣ 建立檔案

1. 在 `AI_Project` 空白處 → 右鍵
2. 新增 → 文字文件
3. 改名為：

```
run_server.bat
```

⚠️ 確認副檔名是 `.bat`，不是 `.txt`

---

### 2️⃣ 編輯內容（完整可直接貼）

```batch
@echo off
CHCP 65001 > nul
TITLE Qwen2.5-Coder-7B Server

:: GTX 1650 / 4GB VRAM 穩定設定
llama-server.exe ^
  -m Qwen2.5-Coder-7B-Instruct-Q4_K_M.gguf ^
  -ngl 99 ^
  -fa ^
  -ctk q8_0 ^
  -ctv q8_0 ^
  -c 6144 ^
  --host 0.0.0.0 ^
  --port 8080

pause
```

存檔後關閉。

---

## 五、啟動模型伺服器

1. **雙擊 `run_server.bat`**
2. 第一次會載入模型（30 秒～1 分鐘）
3. 看到以下訊息代表成功：

```
HTTP server listening on 0.0.0.0:8080
```

👉 **這個視窗不要關**

---

## 六、安裝與設定 AnythingLLM（前端 UI）

### ① 安裝

* 前往：AnythingLLM 官網
* 下載 Windows 版並完成安裝
* 啟動 AnythingLLM

---

### ② 設定 LLM 連線

1. 左下角 ⚙️ **Settings**
2. 點 **LLM Preference**
3. 設定如下（照填）：

| 欄位           | 值                          |
| ------------ | -------------------------- |
| LLM Provider | `Generic OpenAI`           |
| Base URL     | `http://127.0.0.1:8080/v1` |
| API Key      | `123`（隨便）                  |
| Model Name   | `qwen2.5-coder-7b`         |
| Token Limit  | `6144`                     |

4. 點 **Save**

---

## 七、建立重構用 Workspace（RAG）

1. 回到 AnythingLLM 主畫面
2. 建立 Workspace：

   ```
   Code_Refactor
   ```
3. 進入 Workspace

---

### 上傳資料

* 上傳：

  * `.cs / .cpp / .py / .js`
  * 專案資料夾
  * 技術 PDF
* 點 **Save and Embed**
* 等待嵌入完成

---

## 八、正確的「程式重構」提問方式（很重要）

### ❌ 不建議

> 幫我優化這段程式

### ✅ 建議這樣問（效果差很多）

> 請在 **不改 public API** 的前提下
> 重構我剛上傳的 `xxx.cs`
>
> 要求：
>
> 1. 行為必須與原本一致
> 2. 提升可讀性與維護性
> 3. 明確列出你實際修改的地方

👉 **這是 Qwen2.5-Coder-7B 的最佳用法**

---

## 九、效能與穩定性注意事項（GTX 1650）

### 建議關閉

* Chrome / Edge
* Discord
* 遊戲啟動器
* VSCode GPU 加速（可選）

---

### 如果閃退 / OOM

依序嘗試：

1. 把 `-c 6144` 改成 `5632`
2. 把 `-ngl 99` 改成 `-ngl 60`
3. 重新啟動 `run_server.bat`

