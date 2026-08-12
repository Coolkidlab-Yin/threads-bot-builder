# threads-bot-builder — Threads 發文 bot 建造器

> Build a Threads posting bot from any content source — news, your own drafts, long-form repurposing, curation, product updates, replies. Human-in-the-loop by default: you pick the direction in 5 seconds, the AI does the other 20 minutes.

這是一個 Claude Code 教學型 skill:裝了之後直接跟 Claude 說你想做什麼樣的
Threads bot,它會照 SOP 一步步帶你建起來 —— **包含接上瀏覽器陪你把 Meta 的
API 憑證申請下來**,那是多數人卡住的地方。

開場長這樣,不會先丟一堆架構圖給你:

> 你好,我是 coolkid,接下來我會一步一步帶你做出你的第一個 Threads 機器人。
> ……我們先從最有用的一題開始:你希望這個 bot 幫哪個帳號,省下哪一段重複工作?

之後每一輪只推進**一個概念、一個檢查、一個動作**,不會一次把 token、排程、
去重、部署全倒給你。

## 內容從哪來,你自己決定

這份骨架不預設你要發什麼。你講得出用途就直接往下走,講不出來時它會給例子:

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

**Threads API 憑證不用自己研究怎麼申請。** 進 Meta 後台之前,skill 會先
確認 AI 有沒有瀏覽器能力,有的話一頁一頁陪你走到拿到為止 —— 你只負責打
帳密、按送出這些該由你做的動作。

AI 沒有瀏覽器能力時**不會叫你自己想辦法**,它會依自己是哪個工具給你安裝方式:

| 你在用 | 怎麼接 |
|---|---|
| Claude Code(想沿用你已登入的 Chrome) | 裝 [Claude 瀏覽器擴充](https://chromewebstore.google.com/detail/claude/fcoeoabgfenejglbffodgkkbkcdhcgfn),步驟見 [官方說明](https://support.claude.com/en/articles/12012173-getting-started-with-claude-for-chrome) |
| Claude Code(想另開乾淨瀏覽器) | `claude mcp add playwright npx @playwright/mcp@latest` |
| Codex CLI | `codex mcp add playwright -- npx @playwright/mcp@latest` |
| 其他支援 MCP 的工具 | 依它的設定檔加入 [Playwright MCP](https://github.com/microsoft/playwright-mcp) |

兩條路都是透過 MCP 到 AI 手上,差別在**接哪個瀏覽器**:擴充接管你現在
已登入的 Chrome(免重登,但同一個 Chrome 的其他分頁也在它視野內);
Playwright MCP 另開一個乾淨的(要重登一次,但看不到你其他東西)。

都不想裝也行,會改用一次一步的口述模式,一樣做得完,只是慢一些 ——
而且不會每個畫面再問你一次要不要裝。

| 要準備的 | 大約時間 | 要錢嗎 |
|---|---|---|
| 一個 Threads 帳號 | — | 免費 |
| Meta 開發者後台的應用程式與長期 token | 20 分鐘 | 免費,自用不必送審 |
| 一台會一直開著的機器(或雲端排程) | — | 看你選什麼 |

## 已含的實戰坑(原作者實測)

- Windows CP950 編碼:中文塞進 curl 命令列會變亂碼,要改走 UTF-8 檔
- **兩段式發文:create 成功不等於 publish 成功**,兩者的 id 與錯誤要分開記。
  而且 skill 明文禁止「固定 sleep 幾秒就當作容器好了」—— 要照平台可觀察的
  狀態判斷,固定等待不是成功證據
- 長期 token 靜默失效:API 有回錯,但你沒記 log 就不會知道
- 「沒報錯但也沒發」:這類 bot 最難抓的故障型態,log 不是選配

## 硬性 gate(skill 內建,語氣再親切也不會放水)

1. **沒有你的批准,不會真的發文**。dry-run 跑完只能說「完成至 dry-run」,
   不能講成做完了
2. 批准**只算那一篇**,不會延伸到後面的貼文
3. 同一素材連跑兩次,發布前一定被去重擋下
4. 不從記憶編造平台 UI、API 版本、參數、權限或配額 —— 不確定就當場查官方文件

> 內容面的紅線(語氣、字數、要不要放連結、哪些主題不碰)**是你自己定的**,
> skill 會在開工前帶你講清楚並寫進設定,而不是塞一套預設值給你。

## 原作者實跑過的版本

skill 裡附了一份完整的範例 recipe:作者自己在跑的**內容策展**型 bot,
每天 20:00 策展 GitHub 上的好專案。上面所有的坑都是從那個版本踩出來的。
拿它當填法參考,不是你一定要做的東西。

完整實戰記錄(卡點、debug 過程、FAQ)在
[Threads 每天 20:00 自動發文 bot](https://www.coolkidlab.com/workflows/threads-auto-poster.html)。
更多 build-in-public 記錄在 [coolkidlab.com](https://www.coolkidlab.com)。

## License

MIT
