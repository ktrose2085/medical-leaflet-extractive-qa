# 🏥 中文藥品仿單問答系統 — Extractive QA 實驗

> **技術探索**：以 Hugging Face RoBERTa 模型對中文醫療仿單進行抽取式問答（Extractive QA），
> 並透過實驗結果驗證其能力與侷限，為後續導入 RAG + LLM 架構提供依據。

---

## 📌 專案背景

藥品仿單包含適應症、用法用量、禁忌與不良反應等關鍵資訊，但篇幅長且術語艱澀，一般民眾難以快速查找。本專案嘗試以 NLP 技術建立自動問答系統，以安美達錠（Arimidex 1mg，anastrozole）的衛福部核准中文仿單為實驗對象。

---

## 🛠️ 技術架構

| 元件 | 技術 |
|------|------|
| PDF → Markdown 解析 | LlamaParse (LlamaCloud API) |
| 文本切塊 | 自訂 sliding window (chunk=400, overlap=50) |
| QA 模型 | `uer/roberta-base-chinese-extractive-qa` (Hugging Face) |
| 框架 | Hugging Face Transformers + LangChain |
| 執行環境 | Jupyter Notebook (Anaconda, Python 3.13) |

---

## 📊 實驗結果

| 問題 | 答案 | 信心分數 | 評估 |
|------|------|----------|------|
| 這藥的適應症是什麼？ | 治療停經後婦女晚期乳癌 | 0.4547 | ✅ 正確 |
| 怎麼服用？ | 口服 | 0.0964 | ✅ 正確 |
| 不良反應包含哪些？ | 關節炎、關節退化及關節痛 | 0.0084 | ⚠️ 部分正確（表格資料回答不完整）|
| 一次吃幾顆？ | 2 | 0.0001 | ❌ 錯誤（正確應為每天1顆）|
| 什麼人不能吃？ | 狗 | 0.0006 | ❌ 錯誤（需跨段落推論） |

---

## 🔍 發現與侷限

**Extractive QA 的能力邊界：**
- ✅ **擅長**：答案為單一短句且明確出現在文中（如適應症、服用方式）
- ❌ **不擅長**：答案分散於 Markdown 表格（如不良反應清單）
- ❌ **不擅長**：需要跨段落整合推論（如禁忌對象）

**根本原因**：RoBERTa 等 Extractive QA 模型以「連續文字片段」為答案單位，Markdown 表格的 `|` 符號會干擾模型的語意判斷。

---

## 🚀 快速開始

### 1. 安裝套件
```bash
pip install -r requirements.txt
```

### 2. 開啟 Notebook
```bash
jupyter notebook 20260219_Hugging-Face-extractive-QA.ipynb
```

### 3. 依序執行每個 Cell 即可

> **注意**：首次執行時，模型（約 400MB）會自動從 Hugging Face Hub 下載。

---

## 📁 資料來源

- 安美達錠中文仿單：[衛生福利部食品藥物管理署](https://www.fda.gov.tw/)，藥品許可證字號 衛署藥輸字第 022282 號
- PDF 轉 Markdown：使用 [LlamaParse](https://cloud.llamaindex.ai/) 解析，保留原始表格結構

---

## 🔮 後續計畫

本專案為技術探索的 **Phase 1**。針對 Extractive QA 的侷限，計畫導入：
- **Generative QA (RAG)**：結合 Llama-3 + LlamaIndex + Groq API，讓模型能整合表格資訊並生成完整回答
- **向量資料庫 (Vector DB)**：以 ChromaDB 進行語意檢索，取代暴力掃描切片的方式

---

## 👩‍💻 作者

**KTRose**｜生命科學＆翻譯學雙碩士 → 轉職 LLM 工程師  
對 LLM 應用有濃厚興趣。
