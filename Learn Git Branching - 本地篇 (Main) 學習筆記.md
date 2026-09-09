# Learn Git Branching - 本地篇 (Main) 學習筆記
[Git視覺化開源學習網站](https://learngitbranching.js.org/?locale=zh_TW)
<https://learngitbranching.js.org/?locale=zh_TW>

## 1. 基礎篇 (Git 基礎打底)
這裡在教你怎麼創造歷史，以及怎麼把分岔的歷史收束回來。

### 關卡 1：介紹 Git Commit
* **題目目標**：單純往前推進歷史，建立兩個新的 Commit。
* **通關指令**：
  ```bash
  git commit
  git commit
  ```

### 關卡 2：介紹 Git Branch
* **題目目標**：建立一個名為 `bugFix` 的新分支，並把視角切換過去。
* **通關指令**：
  ```bash
  git checkout -b bugFix
  ```
  > 註：這是一步到位的寫法，等同於 `git branch bugFix` 加上 `git checkout bugFix`

### 關卡 3：Git Merge (合併)
* **題目目標**：在 `bugFix` 分支跟 `main` 分支各自開發（各提交一次），最後用 merge 把 `bugFix` 融回 `main`，製造出一個有兩個爸爸的「合併節點」。
* **通關指令**：
  ```bash
  git checkout -b bugFix
  git commit
  git checkout main
  git commit
  git merge bugFix
  ```

### 關卡 4：Git Rebase (重塑歷史)
* **題目目標**：一樣各自開發，但這次不用 merge，而是用 rebase 把 `bugFix` 整個「連根拔起」，接到 `main` 的最尾端，讓歷史變成一條直線。
* **通關指令**：
  ```bash
  git checkout -b bugFix
  git commit
  git checkout main
  git commit
  git checkout bugFix
  git rebase main
  ```

## 2. 進階篇 (在歷史樹上反覆橫跳)
這裡教你怎麼擺脫分支名稱的束縛，直接在節點之間穿梭。

### 關卡 1：分離 HEAD (Detach HEAD)
* **題目目標**：把跟著分支跑的 HEAD 拔下來，直接指到過去的 `C4` 節點上。
* **通關指令**：
  ```bash
  git checkout C4
  ```

### 關卡 2：相對參照 `^` (往上一層)
* **題目目標**：不使用 Hash 值，利用 `^` 切換到 `bugFix` 的父節點。
* **通關指令**：
  ```bash
  git checkout bugFix^
  ```

### 關卡 3：相對參照 `~` (往上 N 層)
* **題目目標**：學會強制移動分支。把 `main` 移到 `C6`，把 `bugFix` 移到 `C0`，並把 HEAD 移到上一層。
* **通關指令**：
  ```bash
  git branch -f main C6
  git branch -f bugFix C0
  git checkout HEAD~1
  ```

### 關卡 4：取消提交 (後悔藥)
* **題目目標**：用 `reset` 抹除本地分支的歷史，用 `revert` 反向抵銷已上傳分支的歷史。
* **通關指令**：
  ```bash
  git reset HEAD~1
  git checkout pushed
  git revert HEAD
  ```

## 3. 調整提交順序 (工程師的偷天換日)
開始變魔術了，教你怎麼把散落的程式碼拼湊成完美的樣子。

### 關卡 1：Cherry-pick (摘櫻桃)
* **題目目標**：你現在在 `main`，把隔壁分支的 `C3`、`C4`、`C7` 這三個節點精準複製過來。
* **通關指令**：
  ```bash
  git cherry-pick C3 C4 C7
  ```

### 關卡 2：互動式 Rebase
* **題目目標**：呼叫出 UI 介面，重新排列前 4 個 Commit 的順序，把不要的丟掉。
* **通關指令**：
  ```bash
  git rebase -i HEAD~4
  ```
  > 註：畫面會跳出一個視窗，用滑鼠把順序排好點擊確定即可。

### 關卡 4：git restore 復原變更
```bash
git restore --staged <file>
```
取消暫存檔案（將檔案移出暫存區，但保留你的變更）
```bash
git restore <file>
```
徹底捨棄檔案變更（請小心，這會刪除這些變更！）

## 4. 活用 Git 的指令 (雜技表演區)
考驗你把前面的技巧融合使用的能力。

### 關卡 1：只取一個 commit
* **題目目標**：分支裡只有一個功能是你要的，把它拉到主線。
* **通關指令**：
  ```bash
  git checkout main
  git cherry-pick C4
  ```

### 關卡 2：提交的技巧 #1
* **題目目標**：發現兩次提交前的 `C2` 寫錯了。用互動式 rebase 把它拉到最前面，用 `--amend` 修改，再重新排回去。
* **通關指令**：
  ```bash
  git rebase -i HEAD~2  # 記得在 UI 把 C2 跟 C3 交換順序
  git commit --amend
  git rebase -i HEAD~2  # 把順序換回來
  git branch -f main HEAD
  ```
* **語法補充**：
 `git branch`：Git 中負責管理分支的子命令，可用於建立、列出、重新命名或刪除分支。
  `-f（同 --force）`：強制（force）選項。如果指定的目標分支已經存在，正常情況下建立同名分支會報錯；加上 -f 後，Git 會略過防呆檢查，直接把該分支的參照指標強行覆蓋或移動到指定位置。
  `main`：要被操作的目標分支名稱。在這裡代表你要移動或重設的指標名稱是 main。
  `HEAD`：指標要移動到的目的地。HEAD 代表你當前檢出（checkout）的工作目錄所在的 commit。此處也可以替換成特定 commit 的 hash（如 c3a1b2）或相對引用（如 HEAD~2）。

### 關卡 3：提交的技巧 #2
* **題目目標**：跟上一關一樣要改舊的 `C2`，但這次不用 rebase，改用 cherry-pick 暴力解。
* **通關指令**：
  ```bash
  git checkout main
  git cherry-pick C2
  git commit --amend
  git cherry-pick C3
  ```

### 關卡 4：Git Tag (里程碑)
* **題目目標**：幫歷史做記號，在特定的 Commit 上打標籤。
* **通關指令**：
  ```bash
  git tag v1 C2
  git tag v0 C1
  git checkout C2
  ```

### 關卡 5：Git Describe
* **題目目標**：了解怎麼查詢「現在離最近的 Tag 有多遠」。
* **語法介紹**：
  ```bash
  git describe <ref>
  ```
  `<ref>`是任何一個可以被 git 解讀成 commit 的位置，如果你沒有指定的話，git 會以你目前所在的位置為準（HEAD）。
  **指令的輸出：** `<tag>-<numCommits>-g<hash>`
  `<tag>`表示的是離`<ref>`最近的 tag， numCommits 是表示這個 tag 離`<ref>`有多少個 commit，`<hash>`表示的是你所給定的 `<ref>`所表示的 commit 的前七個 id。

## 5. 進階主題 (成為真正的強者)
這裡的樹狀圖會長得很恐怖，別眼花。

### 關卡 1：N 次 Rebase
* **題目目標**：把三個散落的分支全部接到 `main` 上，串成一串糖葫蘆。
* **通關指令**：
  ```bash
  git rebase main bugFix
  git rebase bugFix side
  git rebase side another
  git branch -f main another
  ```

### 關卡 2：選擇父節點
* **題目目標**：當一個節點有兩個父節點（Merge 產生的），用 `^1` 或 `^2` 來指定回溯回哪一個祖先。`^1`是第一個父節點(合併後的主分支)，`^2`是第二個父節點(被合併的分支)。`~n`是回朔同一個分支上的 n 個世代祖先。
* **通關指令**：
  ```bash
  git branch -f bugWork HEAD~1^2~1
  ```

### 關卡 3：糾纏不清的分支
* **題目目標**：這是一場大混戰，用最快的速度把指定的 Commit 丟到對應的分支上。
* **通關指令**：
  ```bash
  git checkout one
  git cherry-pick C4 C3 C2
  git checkout two
  git cherry-pick C5 C4 C3 C2
  git branch -f three C2
  ```