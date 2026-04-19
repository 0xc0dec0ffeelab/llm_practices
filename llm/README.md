# LLM

## Part 1：「理解 LLM 內部」到底是在理解什麼？

你可能會想說，LLM 不就是一個很大的 neural network 嗎？對，但是有幾層意思你要分清楚。

**第一層：理解 architecture。**
也就是 Transformer。這個其實就是：有 attention，有 feed-forward，有 layer norm，有 residual connection，這幾個積木疊起來。

**第二層：理解 training。**
LLM 怎麼 train？其實就是 next-token prediction 嘛。你把一句話丟進去，讓它猜下一個字，猜錯了就 backprop，就這樣。

**第三層：理解 forward pass 的每一步在算什麼。**
attention 那個 softmax 到底在做什麼？KV cache 是什麼？token embedding 的維度為什麼是 768？這一層才是真的把「黑盒子打開來看」。

你要先搞清楚你想理解哪一層，再來選你的武器。

## Part 2：推薦的學習路徑

好，我直接給你一條清晰的路，照這樣走就好了。

**Step 1：先看 Andrej Karpathy 的 `makemore` 系列 + `nanoGPT`**

Karpathy 是業界少數真的超會教的人。他有一個 YouTube 系列叫 *Let's build GPT from scratch*，兩個小時，從零把 GPT 的 attention、transformer block 一行一行寫出來。全部是 pure Python + PyTorch，不依賴任何框架。

這個是你最好的起點。沒有之一。

為什麼？因為你看完以後，你會知道 attention 就是在算「這個 token 要去看哪些其他 token、借多少資訊回來」。它不神秘。

**Step 2：自己實作一個小 GPT，能 train 就算成功**

推薦的小實作：char-level language model。就是拿一個小說、拿一個莎士比亞劇本，讓它學著預測下一個「字元」，不是 token，是 character。

為什麼要從 char-level 開始？因為這樣你不需要搞 tokenizer，直接 ASCII 進去，維度小，在你的筆電 CPU 上一樣跑得起來。

你只要能跑出「胡言亂語但隱約有語感的輸出」，你就成功了。

**Step 3：讀懂 Attention Is All You Need**

這篇 2017 年的 paper 就兩件事：多頭 attention 是什麼、encoder-decoder 結構。你讀完 Karpathy 的 code 再回來看這篇，你會發現「哦原來 paper 就是在寫我剛剛打的那些東西」。

## Part 3：`llm.c` 是什麼？

`llm.c` 是 Karpathy 自己寫的一個專案，用途就一行話說完：

> **用最少的抽象層，用 raw C 或 CUDA 把 GPT-2 的 training 從頭到尾實作出來。**

你可能會想說，為什麼要用 C？PyTorch 不是已經很好用了嗎？

對，PyTorch 確實好用。但好用的代價是什麼呢？就是你不知道「矩陣乘法那一步，背後到底 GPU 在做什麼」。

`llm.c` 的目的不是要你換掉 PyTorch，它的目的是讓你把「框架的魔法」剝掉，真的看到：

- forward pass 每一層的矩陣運算長什麼樣子
- backward pass 的 gradient 是怎麼手算出來的
- GPU kernel 是怎麼平行化的

它有幾個特點：

**一、只有一個 C 檔案（`train_gpt2.c`），幾百行。**
你把它打開來，attention 就在那裡，cross-entropy loss 就在那裡，都是原始的記憶體操作。沒有任何魔法。

**二、它是 GPT-2 等級的，不是玩具。**
你可以真的 train GPT-2 (124M parameters)，跑出來的模型是真實可用的，不是只能跑 hello world。

**三、它有 CUDA 版本。**
等你把 CPU 版本的 C code 看懂了，再去看 `.cu` 的 CUDA 版本，你會開始理解為什麼 GPU 快：因為 attention 的矩陣乘法天生就是可以平行的。

---

## Reference
[llm visualization](https://bbycroft.net/llm) \
[dotLLM](https://github.com/kkokosa/dotLLM/tree/main)
