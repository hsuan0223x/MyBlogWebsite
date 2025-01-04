---
title: Cyberspace CTF 2024 - write-up
published: 2024-09-05
description: '紀錄 Cyberspace CTF 的 write-up'
image: 'background.jpg'
tags: [CTF,write-up,Cyber Secutity]
category: 'CTF'
draft: false 
lang: 'zh-tw'
---

## 起源
關於這個 CTF 參加的起源呢，簡單來說就是
貝坦找了我，然後我找了兩個人，貝坦再找了 101 ，這次比賽的 Team 就誕生了
![image](https://hackmd.io/_uploads/SkgItUIUkg.png)
## 心得
第一次打這種難度比較高的 CTF
只解了一題新手題，然後解完還被貝坦告知說可以用Angr解（Orz）
之後要慢慢研究了，在寫這篇時還有很多沒研究完...
## Write-up & 研究結果
分享幾題我有興趣的
### beginner
#### key (rev)
![image](https://hackmd.io/_uploads/BJK8t8II1x.png)
這一題跟 AIS3 pre-exam 的 火拳のエース 有異曲同工之妙  
都有給逆向程式，就直接逆出來爆破就好，~~但是Angr可以秒解~~
#### Baby Pybash (jail)
![image](https://hackmd.io/_uploads/HJZDK88IJe.png)
jail的分類，簡單來說就是 sandbox escape ，我是沒有想過這麼快就會碰上  
但還是來檢討一下，看別人的 Write-up 是說  
看 source code 可以發現，我們被禁止使用了以下這些符號
```
[a-zA-Z*^\,,;\\!@/#?%`"\'&()-+]|[^\x00-\x7F]
```
然後可以思考可以用哪些符號 bypass
之後叫 ChatGPT 幫我們生成
```
bash jail payload for this ctf challenge that only allows "<>.$|= as" and numbers
```
之後利用他所生成的 Payload 去 bypass 掉驗證機制，然後就可以使用基本的指令了
#### Modulus RSA (Crypto)
![image](https://hackmd.io/_uploads/S1FvY888yl.png)
底下的 Write-up3 有全部的答案，我慢慢研究中...
#### ZipZone (Web)
這題是標準的 Zip Slip 漏洞  
一樣有找到 Write-up  
簡單來說，Softlink 到某個文件，再把那個文件塞進 zip 檔案中
### Crypto
#### ezcoppersmith
![image](https://hackmd.io/_uploads/rJADt8LUke.png)
底下的 Write-up3 有全部的答案，我慢慢研究中...
### Misc
#### REDACTED (OSINT)
![image](https://hackmd.io/_uploads/B1r_YIUI1g.png)
題目有給圖，要我們找出截圖的人  
然後他的 About Me 有 Flag  
這題...出題者是說用 Discord Server ID 去找到伺服器  
進去之後查關鍵字就有了，我直接三條線 = =|||
### Rev
#### snake
![image](https://hackmd.io/_uploads/HJ2dK8LUyx.png)
貪食蛇小遊戲，需要我們吃到 10000 分  
一開始的想法是逆向修改，但後來放棄了  
實際上確實是需要修改，但是要找工具  
好像 Cheat Engine 也可以？
### Web
#### Feature Unlocked 
![image](https://hackmd.io/_uploads/S1ZYYIIIJe.png)
底下的 Write-up2 有全部的答案，我慢慢研究中...
#### Trendz
![image](https://hackmd.io/_uploads/B1UYYULUye.png)
底下的 Write-up2 有全部的答案，我慢慢研究中...
#### Trendzz
![image](https://hackmd.io/_uploads/r1TtFLLUkg.png)
底下的 Write-up2 有全部的答案，我慢慢研究中...
#### Trendzzz
![image](https://hackmd.io/_uploads/HkV5FIL8kg.png)
底下的 Write-up2 有全部的答案，我慢慢研究中...
## Write-up 參考網站
[Write-up參考1](https://0x0w1z.medium.com/this-challenge-was-beginner-challenge-in-cyberspace-ctf-2024-in-this-challenge-we-need-to-bypass-5cfa6f802539)  
[Write-up參考2](https://www.cnblogs.com/LAMENTXU/articles/18396244)  
[Write-up參考3](https://blog.csdn.net/weixin_52640415/article/details/141830026)  
[Write-up參考4](https://medium.com/@ELJoOker/cyberspace-ctf-2024-reverse-engineering-challenges-4226b9055547)  