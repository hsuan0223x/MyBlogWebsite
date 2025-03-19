---
title: BroncoCTF 2025 write-up
published: 2025-02-25
description: '紀錄在 BroncoCTF 2025 中所解的一些題目'
image: 'CTF.png'
tags: [CTF,write-up,Cyber Secutity]
category: 'CTF'
draft: false 
---
## 心得
這次是跟 ICEDTEA 一起打，對於這次的 CTF 我覺得題目沒有出得很好  
感覺學不到什麼東西，是有經驗累積，但是感覺很多題目都需要運氣  
但也大概有感覺，一個好的題目大概是怎麼樣的形式或想法
![rank](https://static.hsuan0223x.com/broncoCTF/rank.png)
[題目連結](https://github.com/SCUBroncoSec/BroncoCTF-2025-Public/tree/main)

## Beginner

### At Least It's Not Pandora

這一題會給你一個 Spotify 歌單網址，然後你只要把歌單反向排序，之後取首個字母  
就是 Flag

### Simon Says

這一題是 steganography 的題目，有在這個 CTF 的考古題中看到了[這個網址](https://georgeom.net/StegOnline/upload)  
把圖片傳上去，然後把 browse bit planes 按下去  
這個東西是灰階位圖的分析，然後把他轉到 Blue 2 就有 Flag 了
因為題目有說 "the last 2 lights have been blue"

### Straight Up Circular (未在賽中解出)

簡單來說，這個就是以某個符號為中心然後右拿一個左拿一個  
所以我們可以看到這個字串 `dvlby_otspnr{cobrnot450i1nm_e03}`
他的中間是 `b` 這個符號，所以他到最後就會解回來 Flag
我覺得這題出給 beginner 很不好，一方面打擊信心，另一方面也不常出現

### Too-Many-Emojis

這一題要解的話是查找 emoji 的首字母，透過 [這個wiki](https://emojipedia.org/)
之後一個一個查出來就有 Flag 了

## Web

### Grandmas Secret Recipe

這一題就是修改 cookie 跟 checksum (md5) 就可以取得 Flag 了

### marys-lamb

這一題是我覺得裡面最不應該被歸類為 Web 的題目  
而且這一題出得很差，[詳解在這](https://github.com/SCUBroncoSec/BroncoCTF-2025-Public/blob/main/Challenges/Web/marys-lamb/solution.md)

## OSINT

### April 25 (賽後解出)

去他們的 IG 找到　April 25 的研討會貼文

### Elite Stacker (未解出)

這題我覺得蠻酷的，是要將 `.ttr` 匯入到 `TETR.IO` 中
然後去翻個人檔案，[詳解在這](https://github.com/SCUBroncoSec/BroncoCTF-2025-Public/blob/main/Challenges/OSINT/Elite%20Stacker/Solution.md)

### Secure Copy Shenanigans

看題目，大概知道跟 SCP 有關，但是不是 Linux 的 SCP (Secure Copy)  
是跟 SCP 基金會有關，然後底下是 SCP 編號，把全部找出來取首字母或符號就是 Flag 了