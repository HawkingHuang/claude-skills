---
name: commit
description: 依照已 stage 的變更產生一則英文 Conventional Commits 訊息，經確認後才執行 commit。只在使用者明確輸入 /commit 時使用。
---

# Commit

依照**已經 stage 的變更**產生一則 commit message，經使用者確認後執行。

## 最重要的前提：不要動 staging area

要 commit 什麼由使用者自己決定，staging area 就是使用者的意思表示。

- **絕對不要執行 `git add`**，任何形式都不行（包含 `git add -A`、`git add .`、`git add <檔案>`）
- **絕對不要 `git stash`、`git checkout`、`git restore`** 或任何會改變工作區狀態的指令
- 未 stage 的變更、未追蹤的檔案，一律當作不存在
- 如果覺得有東西應該一起 commit，用一句話講出來就好，不要自己加進去

自動 `git add` 一旦帶入不該進版控的內容（例如除錯用的檔案、寫死的金鑰、暫時註解掉的程式碼），事後清理的成本遠高於自行 stage 一次。

## 執行流程

### 1. 確認有東西已經 stage

```bash
git status --short
```

staging area 是空的就停下來，告訴使用者「目前沒有已 stage 的變更」，**不要繼續往下走。**

### 2. 讀取已 stage 的變更

```bash
git diff --staged --stat    # 先看範圍
git diff --staged           # 再看內容
```

注意是 `--staged`，不是 `git diff`。用錯的話會拿到未 stage 的變更，寫出來的 message 就跟實際 commit 的內容不符。

diff 很大時先看 `--stat` 判斷主要動到哪些模組，再挑重點檔案細看，不需要逐行讀完。
如果看過重點檔案後仍抓不到明確意圖，直接跟使用者確認這次改動的目的，不要用臆測的描述硬寫。

### 3. 寫出 message

看的是**變更的意圖**，不是變更的動作。

從 diff 判斷「這次改動想達成什麼」，而不是列出動了哪些檔案。檔案清單 git 本身就有記錄，message 要補的是 git 看不出來的那部分。

### 4. 給使用者確認

先用 code block 完整顯示 message（subject、body 分行），再用可點選的選項（例如 AskUserQuestion）讓使用者確認，選項只需簡短標示動作（如「確認 commit」「修改 message」），不要把整段 message 塞進選項文字裡。不要只用文字要求使用者手動輸入「是」之類的回覆。

**不要在使用者確認前執行 `git commit`。**

### 5. 執行

使用者確認後才 commit。**commit 完就結束，不要 push，也不要建議使用者 push。**

使用者可能會直接回覆修改後的 message，或指出要改哪裡；照改之後再確認一次。

## Message 格式

```
type: subject

body（選用）
```

**type** — 依 Angular commit convention 的分類，選最貼近變更意圖的一個：`feat`、`fix`、`refactor`、`perf`、`style`、`docs`、`test`、`build`、`chore`。

**subject** — 規則：

- 一律英文
- 祈使句現在式：`add`、`fix`、`update`，不是 `added`、`fixes`
- 冒號後小寫開頭
- 結尾不加句號
- 控制在 50 字元內，最多不超過 72

**body** — 一律英文。只在「為什麼這樣改」不明顯的時候才寫。純粹重述 subject 的 body 不如不寫。有寫的話與 subject 之間空一行，每行不超過 72 字元。

不要加任何署名或工具標記的 footer（`Co-Authored-By`、`Generated with` 之類的）。

## 範例

**變更**：關閉取消預約跳窗時，重置驗證器的欄位值，避免殘留上一次的驗證狀態

```
fix: reset validator state when closing cancel appointment dialog
```

**需要 body 的情況**：修法看起來繞路，但有原因

```
fix: add Sentry.stop() to release its watcher

BlockEditor rows are added and removed dynamically, and each row
registers a watcher through Sentry. Without an explicit way to stop
it, removed rows kept their watchers alive, leaking watchers as
users kept editing.
```
