---
title: HITCON Community 2024 心得
published: 2024-08-24
description: '分享 2024 年，台灣駭客年會 - 社群場的參加心得'
image: 'HIT2024.png'
tags: [review,community,life]
category: 'Review'
draft: false 
---

今年 2024 的 HITCON CMT 的官網在 [這裡](https://hitcon.org/2024/CMT/)  
然後是在中研院的人文社會館舉辦，有許多 AIS3 獲得最佳專題的學員有收到公關票  
~~而我只能花 2000 元買票進來（哭）~~  
接下來會回顧一下這兩天的內容！
## Day 1
Day 1 我沒有聽議程，主要都是把攤位逛過一遍，然後去跟人聊天
### 成大資安社
~~自家戰隊的攤位肯定要支持的吧~~  
我有玩了成大資安社的闖關遊戲（弱化前），通關的話可以獲得神奇吊飾！  
~~所以我大概有兩個小時都坐在中電會旁邊的椅子上在破遊戲~~  
這邊可以淺提一下解法  
- 第一關
    破解PDF密碼，直接用Kali去爆密碼出來就有ㄌ
- 第二關
    拼圖，然後找下一關的path
    主要是我拼圖拼完了，然後還觸發波蘭牛彩蛋，真的是超扯的
    然後經過了一點提示才知道，要再把 path 拿去 Base64 Decode 才會得到正確的 path 
- 第三關
    進去會看到指示，要利用擁有牛數最多的國家的IP（有提示，是印度）連進去第三關的網頁
    所以我用了 [VPN Gate](https://www.vpngate.net/cn/) 去用了印度的IP連
    然後就指示到成大資安社的官網去
- 第四關
    這邊算是最簡單也是最難的一關，為什麼會這麼說？
    因為這題沒有技術也可以解，然後大家一定都會想不到
    我也是問了提示才知道，最後把所有按鈕點過一次，一定可以通關ㄉ  

最後有拿到吊飾！好棒！
### NISRA
我也有去逛了 NISRA 這個老牌的資安社團的攤位  
有興趣的可以去 Google 看看  
他們攤位的題目主要都是跟他們的社團有關聯  
像是：創立年分、全名是什麼等等  
然後還要加 Discord 拿 Flag  
雖然只有拿到 70 分，但玩得很開心！  
特別感謝 貝坦 跟 101 的提示，才能讓我拿到 Discord 的 Flag www
### 其他的攤位跟總結
還有像是 TSC&逢甲黑客社、DEVCORE、等等的攤位都很好玩  
也讓我知道 HITCON 不只是只有技術，還有交流成分 www  
~~然後為什麼 AIS3 跟 AIS3 Club 還有 HITCON 的人，重疊率很高欸~~  
第一天大概就逛逛攤位，然後當個貼紙蟑螂(X)
## Day 2
Day 2 我主要都是聽議程，我總共聽了三場
接下來我都會分別紀錄一下大概的內容還有心得
### 你的雲端攝影機安全嗎？
這場議程是中華資安國際的王凱慶講師所主講的  
主要是在講說家中的 Web Cam 他是怎麼去做分析然後破解  
他講的分析概念可以套用到現在的IoT設備上，~~雖然我沒有很認真聽~~  
但我覺得最搞笑的是，分析了半天，然後發現韌體的 Source code 公司方沒有鎖  
但還是有受教了，有認識到 IoT 攻擊的手法  
### Background of those glitches in Zelda BoTW & ToTK
接下來這一場議程是由 [Yuda](https://www.youtube.com/channel/UCls1auujdqxmOoM7C4TUgng) 所發表的  
我認為這場議程相對於其他議程來說很特殊，主要原因有以下  
1. 講師不是資安圈內人
    ~~但我知道講師是 Zelda Speedrun 圈內知名的人物~~
2. 技術面的含量極低
    主要是為了讓各位大神能夠理解遊戲的機制、內容等等
    所以 Yuda 也有盡量把遊戲內容簡化
    用淺顯易懂的方式講給我們會眾聽

總結來說，他其實更多是在講述漏洞挖掘的過程，比較不像是一般的技術研究  
這其實也勉勵了像是我一樣的資安新手在這段路途中繼續走下去  
~~然後為什麼講師的簡報可以做這麼好~~
大概有做了一下筆記！
### Confusion Attacks: Exploiting Hidden Semantic Ambiguity in Apache HTTP Server!
這場議程主要是紀錄我們的臺灣傳奇滲透師 - Orange 所憋出來的大招w  
這一篇議程也有在 DEVCORE 的 Blog 上記錄 [連結在這](https://devco.re/blog/2024/08/09/confusion-attacks-exploiting-hidden-semantic-ambiguity-in-apache-http-server/)  
~~然後有跟 Orange 合到照，超開心~~  
這場議程我覺得以我現在的技術能力，絕對是聽不懂的w  
但是我認為這樣也是一個經驗，可以學習一下大佬是怎麼思考的  
當然技術也是很重要的一部分，~~所以我決定回家多練~~  
## 總結
這次 HITCON CMT 的體驗很棒，如果你有很多朋友  
可以跟你一起逛攤位、一起聽議程、~~一起耍廢~~  
那就非常歡迎來 HITCON CMT 一起參與這場電神聚會  
最後一點小小的心得  
老實說會擔心聽不懂議程或者參與不了，~~但只要有朋友在都好說~~  
有些人可能會想說，為什麼沒有 Badge 跟 Re:CTF 的講解或介紹  
因為…我都沒有打 www  