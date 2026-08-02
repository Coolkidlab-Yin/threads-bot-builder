# threads-bot-builder — Threads 發文 bot 建造器

> Build a Threads posting bot from any content source — news, your own drafts, long-form repurposing, curation, product updates, replies. Human-in-the-loop by default: you pick the direction in 5 seconds, the AI does the other 20 minutes.

這是一個 Claude Code 教學型 skill:裝了之後直接跟 Claude 說你想做什麼樣的
Threads bot,它會照 SOP 一步步帶你建起來 —— **包含開瀏覽器陪你把 Meta 的
API 憑證申請下來**,那是多數人卡住的地方。

## 內容從哪來,你自己決定

這份骨架不預設你要發什麼。skill 的第一步就是問你要做哪一種:

| 型態 | 內容從哪來 |
|---|---|
| 內容策展 | 你關注的來源:專案、論文、新品、他人貼文 |
| 新聞快訊 | RSS / 新聞 API,有大事才發 |
| 存稿排程 | 你自己先寫好的稿池 |
| 長內容改寫 | 你的文章 / 電子報 / 影片 |
| 產品動態 | changelog、release、里程碑 |
| 互動回應 | 收到的留言、提問、提及 |
| **以上都不是** | **你講,照你的做** |

選哪一種只影響三個步驟(問方向、選材、去重鍵),**骨架完全一樣**:
排程觸發 → 問你方向 → 選材去重 → 依你的語氣寫文 → 兩段式發文 → 記 log。

## 核心設計:人在迴路,不是全自動

1. 固定時間(或事件)觸發
2. 第一件事不是發文,是**問你一題**(可以直接回「今天跳過」)
3. 你點一下之後,選材、去重、寫文、發文全自動
4. 你出 5 秒判斷,省下每次二十幾分鐘;而且每篇都是你看過方向的

全自動發文很容易吐出一堆機器味、沒人看過的內容,反過來砸掉帳號可信度。
**自動化可以省力,但不能拿可信度去換** —— 這是整個 skill 的立場。
你堅持要全自動的話 skill 會尊重,但會先講清楚風險。

## 安裝

```
/plugin marketplace add Coolkidlab-Yin/Coolkidlab
/plugin install threads-bot-builder@coolkidlab
```

裝完直接說「我想做一個 Threads 自動發文 bot」,Claude 會先問你要做哪一種。

> **這是教學型 skill,不是現成的 bot。** repo 裡沒有可以直接跑的程式碼 ——
> 它教 Claude Code 帶著你把 bot 從零寫出來,包含怎麼申請憑證。

## 開工前你要先有的東西

**Threads API 憑證不用自己研究怎麼申請。** skill 的第 0.5 步會讓 AI 開
瀏覽器,一頁一頁陪你走到拿到為止 —— 你只負責打帳密、按送出這些該由你
做的動作。(AI 沒有瀏覽器能力的話會改用一次一步的口述模式,一樣做得完。)

| 要準備的 | 大約時間 | 要錢嗎 |
|---|---|---|
| 一個 Threads 帳號 | — | 免費 |
| Meta 開發者後台的應用程式與長期 token | 20 分鐘 | 免費,自用不必送審 |
| 一台會一直開著的機器(或雲端排程) | — | 看你選什麼 |

## 已含的實戰坑(原作者實測)

- Windows CP950 編碼:中文塞進 curl 命令列會變亂碼,要改走 UTF-8 檔
- 兩段式發文的時序:create container 後直接 publish 會失敗,中間要 sleep
- token 60 天靜默失效:API 有回錯,但你沒記 log 就不會知道
- 「沒報錯但也沒發」:這類 bot 最難抓的故障型態,log 不是選配

## 紅線(skill 內建,每篇都守)

1. 結尾不加「追蹤我」這類 CTA 或 slogan
2. 文中不放聯盟連結或 UTM
3. 整篇不超過 500 字(寫文控在 480 內留 buffer)

## 原作者實跑過的版本

skill 裡附了一份完整的範例 recipe:作者自己在跑的**內容策展**型 bot,
每天 20:00 策展 GitHub 上的好專案。上面所有的坑都是從那個版本踩出來的。
拿它當填法參考,不是你一定要做的東西。

完整實戰記錄(卡點、debug 過程、FAQ)在
[Threads 每天 20:00 自動發文 bot](https://www.coolkidlab.com/workflows/threads-auto-poster.html)。
更多 build-in-public 記錄在 [coolkidlab.com](https://www.coolkidlab.com)。

## License

MIT
