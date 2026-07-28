# threads-auto-poster — Threads 半自動發文 bot 教學 skill

> A human-in-the-loop Threads posting bot recipe for Claude Code: scheduled trigger, you pick the direction in 5 seconds, AI does the other 20 minutes.

這是一個 Claude Code 教學型 skill:當你想做「每天定時發 Threads、但發什麼方向由你決定」的半自動 bot,裝了這個 skill 之後直接跟 Claude 講,它會照 SOP 一步步帶你建起來。

核心設計是**人在迴路**,不是全自動:

1. 每天固定時間 scheduled-tasks 自動觸發
2. 先跳一題問你今天想看哪個方向(可以直接跳過)
3. 抓 GitHub trending / search 選題、比對 30 天 log 去重
4. 依你的語氣規則寫繁中介紹文
5. Threads Graph API 兩段式發文(create container → sleep → publish)
6. long-lived token 每週自動 refresh(60 天靜默失效的解法)

已含原作者實測踩過的坑:Windows CP950 編碼、兩段式發文時序、token 靜默失效、「沒報錯但也沒發」的 log 對策。

## 安裝

```
/plugin marketplace add Coolkidlab-Yin/Coolkidlab
/plugin install threads-auto-poster@coolkidlab
```

裝完之後直接說「我想做一個 Threads 自動發文 bot」,Claude 會照 skill 的 SOP 引導。

## 紅線(skill 內建,每篇都守)

1. 結尾不加「追蹤我」這類 CTA 或 slogan
2. 文中不放聯盟連結或 UTM
3. 整篇不超過 500 字(寫文控在 480 內留 buffer)

自動化可以省力,但不能拿帳號可信度去換——這是整個 skill 的立場。

## Credits

完整的實戰記錄(卡點、debug 過程、FAQ)在原文:[Threads 每天 20:00 自動發文 bot:排程 + 選題 + Graph API](https://www.coolkidlab.com/workflows/threads-auto-poster.html)。更多 build-in-public 記錄在 [coolkidlab.com](https://www.coolkidlab.com)。

## License

MIT
