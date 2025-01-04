---
title: CGGC 2024 - write-up
published: 2024-11-04
description: '在 CGGC 中所解出來的題目 write-up'
image: 'background.png'
tags: [CTF,write-up,Cyber Secutity]
category: 'CTF'
draft: false 
lang: 'zh-tw'
---

## Rev  

### lazy7  

題目有給我們檔案  
先來看看python程式  

#### pack.py  

```python
from subprocess import Popen, PIPE
from base64 import b64encode

log = open("./output", "wb")

with open("./flag.png", "rb") as f:
    flag = f.read()
    flag = b64encode(flag)

with Popen(["./lazy7", flag], stdout=PIPE, stdin=PIPE, stderr=PIPE) as proc:
    output = proc.stdout.read()
    log.write(output)
```  

可以看到，他先打開了 `flag.png` 做 base64 編碼  
然後再執行 `lazy7` 然後把 結果寫入到 `output.txt`  
可以打開 `lazy7` 看看他在搞什麼花招  
這邊只列一些比較關鍵的code  

```c
v8 = a2[1];
v4 = sub_1269(v8, &ptr);
v9 = malloc(0x100000uLL);
sub_14D5(ptr, v4, v9);
v5 = sub_1269(v9, &v7);
v10 = malloc(0x100000uLL);
sub_14D5(v7, v5, v10);
printf("Output Data: %s\n", (const char *)v10);
```

上面可以看到，呼叫了 `sub_1269` 跟 `sub_14d5` 這兩個函式  
繼續解析

##### sub_1269

```c
  v14 = strlen(a1);
  v8 = 0;
  v9 = 0;
  *a2 = malloc(12LL * v14);
  while ( v9 < v14 )
  {
    v10 = 0;
    v11 = 0;
    v2 = v9;
    if ( v9 < 255 )
      v2 = 255;
    for ( i = v2 - 255; i < v9; ++i )
    {
      for ( j = 0; v14 > v9 + j && a1[i + j] == a1[v9 + j] && j <= 254; ++j )
        ;
      if ( j > v10 )
      {
        v10 = j;
        v11 = v9 - i;
      }
    }
    v3 = v8++;
    v4 = (__int64)*a2 + 12 * v3;
    if ( v10 <= 0 )
    {
      v6 = a1[v9];
      *(_DWORD *)v4 = 0;
      *(_DWORD *)(v4 + 4) = 0;
      *(_BYTE *)(v4 + 8) = v6;
      ++v9;
    }
    else
    {
      v5 = a1[v9 + v10];
      *(_DWORD *)v4 = v11;
      *(_DWORD *)(v4 + 4) = v10;
      *(_BYTE *)(v4 + 8) = v5;
      v9 += v10 + 1;
    }
  }
  return v8;
```

##### sub_14d5

```c
  v5 = 0;
  for ( i = 0; ; ++i )
  {
    result = i;
    if ( (int)i >= a2 )
      break;
    v6 = sprintf(
           (char *)(v5 + a3),
           "%02X%02X",
           (unsigned __int8)BYTE1(*(_DWORD *)(12LL * (int)i + a1)),
           (unsigned __int8)*(_DWORD *)(12LL * (int)i + a1))
       + v5;
    v7 = sprintf(
           (char *)(v6 + a3),
           "%02X%02X",
           (unsigned __int8)BYTE1(*(_DWORD *)(12LL * (int)i + a1 + 4)),
           (unsigned __int8)*(_DWORD *)(12LL * (int)i + a1 + 4))
       + v6;
    v5 = sprintf((char *)(a3 + v7), "%02X", *(unsigned __int8 *)(12LL * (int)i + a1 + 8)) + v7;
  }
  return result;
```  

 `sub_1269`：LZ77壓縮算法  
 `sub_14d5` ：2進制轉16進制  
所以我們需要做的事情是這樣  

1. 16 -> 2
2. LZ77解壓
3. 16 -> 2
4. LZ77解壓
5. base64 decode

不多說，上code

### decrypt script

```python
from base64 import b64decode

def hex_decode(hex_str):
    """將十六進制字串轉換回壓縮單元列表"""
    units = []
    i = 0
    while i < len(hex_str):
        # 每 10 個字符為一個單元
        unit_hex = hex_str[i:i+10]
        if len(unit_hex) < 10:  # 處理最後不完整的單元
            break
            
        # 解析壓縮單元
        offset = int(unit_hex[0:4], 16)  # 前 4 個字符是偏移量
        length = int(unit_hex[4:8], 16)  # 中間 4 個字符是長度
        next_char = bytes.fromhex(unit_hex[8:10])  # 最後 2 個字符是下一個字符
        
        units.append((offset, length, next_char))
        i += 10
    return units

def lz77_decompress(compressed_units):
    """LZ77 解壓縮"""
    result = bytearray()
    
    for offset, length, next_char in compressed_units:
        # 如果有需要複製的內容 (length > 0)
        if length > 0:
            # 從已解壓的內容中複製
            start = len(result) - offset
            for i in range(length):
                result.append(result[start + i])
        # 添加下一個字符
        result.extend(next_char)
    
    return bytes(result)

def decrypt_file(input_file, output_file):
    """解密完整文件"""
    # 讀取加密數據
    with open(input_file, 'rb') as f:
        encrypted_data = f.read().decode('ascii').strip()
    
    # 第一次 HEX 解碼和 LZ77 解壓縮
    units1 = hex_decode(encrypted_data)
    decompressed1 = lz77_decompress(units1)
    
    # 第二次 HEX 解碼和 LZ77 解壓縮
    units2 = hex_decode(decompressed1.decode('ascii'))
    decompressed2 = lz77_decompress(units2)
    
    # Base64 解碼
    final_data = b64decode(decompressed2)
    
    # 寫入結果
    with open(output_file, 'wb') as f:
        f.write(final_data)

if __name__ == "__main__":
    # 使用示例
    decrypt_file("output", "decrypted_flag.png")
```

## Misc  

### Day31- 水落石出！真相大白的十一月預告信？  

點進去發現是鐵人賽文章  
可以在 `day19` 發現了 Telegram bot 的 api 洩漏  
然後路徑換 getUpdate 查找關鍵字就有了  

## 心得

看到這段其實感觸蠻多的

![image1](/CGGC2024/image1.png)
![image2](/CGGC2024/image2.png)

讓我想起了學資安的路上最基本的就是  
解題目 -> 寫&讀 writeup -> 培養思路 化成經驗值  
然後一直反覆，慢慢變強