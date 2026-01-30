# The Story behind cmdx

有一天，我買了一台樹莓派。

接上螢幕、插上電源，看著黑底白字的終端機畫面，我輸入了第一個指令：

```bash
ls
```

目錄列出來了。我接著試了 `cat`、`grep`、`pwd`⋯⋯ 這些指令在 Linux 上如此自然。後來我又接觸了 macOS，發現這些指令也通用。原來，**它們都屬於 Unix 家族**。

---

## Unix vs Windows：兩個世界的指令

回到 Windows，我打開命令提示字元，習慣性地輸入 `ls`：

```
'ls' is not recognized as an internal or external command
```

欸？

原來 Windows 有自己的一套指令系統，跟 Unix 完全不同：

| 你想做的事 | Unix 指令 | Windows 指令 |
|-----------|----------|-------------|
| 列出檔案 | `ls` | `dir` |
| 顯示檔案內容 | `cat` | `type` |
| 搜尋文字 | `grep` | `findstr` |
| 複製檔案 | `cp` | `copy` |
| 移動/重命名 | `mv` | `move` |
| 刪除檔案 | `rm` | `del` |
| 顯示當前目錄 | `pwd` | `cd`（不帶參數）|
| 清除畫面 | `clear` | `cls` |

**兩套系統，兩種語言。** 如果你同時使用 Linux 和 Windows，腦袋就得不斷切換。

---

## 發現 WSL，但⋯⋯

後來我發現了 WSL（Windows Subsystem for Linux），在 Windows 裡跑 Linux！

問題解決了嗎？沒有。

WSL 是一個完整的 Linux 環境，意味著你需要：
- 切換到 WSL 終端
- 在兩個檔案系統間轉換路徑（`/mnt/c/Users/...` vs `C:\Users\...`）
- 處理 Windows 程式和 Linux 程式的互通問題

**我只是想打個 `ls`，不是想開另一台電腦。**

---

## 痛點：肌肉記憶 vs 現實

真正的痛點是這樣的：

當你習慣了 Unix 指令，手指會自動輸入：

```bash
ls -la              # 想看詳細檔案列表 → Windows 不認識
cat README.md       # 想快速看檔案 → Windows 要用 type
grep "error" log    # 想搜尋錯誤訊息 → Windows 要用 findstr
rm *.tmp            # 想刪除暫存檔 → Windows 要用 del
```

每一次，你的手打完，螢幕上出現紅字錯誤，你才想起：「啊，這是 Windows。」

然後你嘆口氣，把指令改成 Windows 版本。

**日復一日，這個摩擦成本累積起來，相當煩人。**

---

## 解決方案：讓兩個世界互相聽懂

如果 Windows 能直接認得 `ls`、`cat`、`grep` 這些指令呢？

這就是 **cmdx** 的目標：

> **在 Windows 上，用你習慣的 Unix 指令。**

不需要安裝 WSL，不需要切換 Shell，不需要改變習慣。
打 `ls`，就列出檔案。打 `cat`，就顯示內容。

而且，如果你安裝了更強的現代工具（像 `eza`、`bat`、`ripgrep`），cmdx 會自動使用它們。

---

## 反過來呢？

當然，也有人是相反的情況：

**從 Windows 轉到 Linux，卻一直想打 `dir`、`cls`、`type`。**

所以我做了姊妹專案：

| 專案 | 解決的問題 |
|-----|-----------|
| **cmdx** | 在 Windows 上使用 Unix 指令 |
| [**wcmd**](https://github.com/Jeffrey0117/wcmd) | 在 Linux 上使用 Windows 指令 |

同樣的設計哲學，同樣的解決方式，只是方向相反。

無論你從哪個系統來，都能用熟悉的方式工作。
