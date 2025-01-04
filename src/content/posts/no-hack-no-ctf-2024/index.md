---
title: No Hack No CTF 2024 - write-up
published: 2024-11-21
description: '在 NHNC 中所解出來的題目 write-up'
image: 'CTF.png'
tags: [CTF,write-up,Cyber Secutity]
category: 'CTF'
draft: false 
lang: 'zh-tw'
---

## Misc
### Blog 2
![image](https://hackmd.io/_uploads/ry7uhhhG1x.png)

貼文翻一翻會看到討論區
![image](https://hackmd.io/_uploads/r1H16n3Mke.png)

點進去會看到這個
![image](https://hackmd.io/_uploads/SJEWTnhMJe.png)

進去 cyber-user-meow 就有 Flag 了
![image](https://hackmd.io/_uploads/Sksh6nhMJl.png)

### Where is this

圖片放大可以看到
![image](https://hackmd.io/_uploads/H1xbg0n3Mke.png)

把店名丟到 google map 找找經緯度就有 flag 了

### NHNC, but C0LoRfUl

進去以後，看看東西，有身分組可以玩，把自己加上 staff
![image](https://hackmd.io/_uploads/HJwika3zJe.png)

然後發現了flag頻道，也有發現base64編碼的flag
![image](https://hackmd.io/_uploads/ry0CkThfkl.png)

## Forensics

### MagicButton

apktools 解出來
assets/Lemontea.json裡就有flag

## Crypto

### AES?

```
Enter IV: 1234567890987654
Secret Key: 1234567890987654
output: TkdU8sqjliuakA+nj2aEmbDf+AaJwASfPuooaKadCqg=
```

直接餵隨便一個 AES 解密網站

### Secret ROT13

solved scripts:
```python=
def decrypt(encrypted_text, key):
    decrypted_text = ""
    for i, char in enumerate(encrypted_text):
        offset = ((i + 1 + key) * (i + 1)) % 26
        if 'A' <= char <= 'Z':
            new_char = chr((ord(char) - ord('A') - offset) % 26 + ord('A'))
        elif 'a' <= char <= 'z':
            new_char = chr((ord(char) - ord('a') - offset) % 26 + ord('a'))
        else:
            new_char = char
        decrypted_text += new_char
    return decrypted_text

key = 7
ciphertext = "VZRU{Y0k_yd0w_Z0o_ti_rsslyxli}"
plaintext = decrypt(ciphertext, key)
print("解密後的明文:", plaintext)
```

## Web

### 哥布林保衞部公告

F12按下去翻一翻

### EASY METHOD

![image](https://hackmd.io/_uploads/r1nS-a2f1g.png)

要送出 PUT requests
用 kali 然後打上　``curl -X PUT http://23.146.248.227:60001/``

### I need to get the C00kies

一進去看到有東西可以點，點看看
![image](https://hackmd.io/_uploads/Hyo0-TnMJx.png)

OK，我們不是 admin

之後按 F12 > Application > cookie 更改掉參數
就會噴Flag了

![image](https://hackmd.io/_uploads/SJsNGT3Mkx.png)

### login

**SQL inject 'or'1'='1**

### 1 line php

source code:
```php=
<?php system('# '.$_GET['cmd']); highlight_file('index.php'); ?>
```
一般來說，不管cmd帶什麼參數都會被#字號吃掉，所以要設法繞過這個限制
`#` 在 php 中代表`單行註解` 所以我們可以嘗試利用換行去繞過
之後試一試發現 `%0A` 是關鍵，在URL編碼中代表著換行
所以我們可以逐步地去追蹤根目錄然後找到flag了


### Democracy
![image](https://hackmd.io/_uploads/HynSmpnfkx.png)

按下去按鈕，發現跳轉順序是 index > next.html > rickroll(youtube)
所以可以利用 burp 去抓包，然後看到了神奇的東西

![image](https://hackmd.io/_uploads/SJ_KNahfyl.png)

把它解密就有Flag了

## Reverse

### easyyyyyyyyyy

打開靜態分析工具，看到flag函式中的內容就是flag了

### Here's the sauce

solve script:

```c
#include <stdio.h>
#include <stdlib.h>

// 已知的密文
unsigned char encrypted[16] = {144, 235, 117, 99, 28, 117, 208, 251, 96, 9, 0, 118, 144, 77, 204, 222};
unsigned char flag[16];

unsigned int f() {
    unsigned int a = 0x001337AA;
    unsigned int b = 0xFF133700;
    unsigned int c = 0xAA1337FF;
    unsigned int d = 0xCC1337D5;
    return (a & b & c & d);
}

int main() {
    // 計算相同的種子
    unsigned int seed = f() >> 8;
    printf("Seed value: 0x%x\n", seed);
    
    // 設定相同的隨機種子
    srand(seed);
    
    // 解密過程
    printf("Decrypted flag: ");
    for(int i = 0; i < 16; i++) {
        // 生成相同的隨機數
        unsigned char rand_val = rand() % 0xff;
        // 解密：密文與隨機數做異或運算
        flag[i] = encrypted[i] ^ rand_val;
        printf("%c", flag[i]);
    }
    printf("\n");

    // 以十六進制顯示
    printf("\nHex values:\n");
    for(int i = 0; i < 16; i++) {
        printf("0x%02x ", flag[i]);
    }
    printf("\n");
    
    return 0;
}
```

## Pwn
### Grading system

看 source code
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

void spawn_shell() {
	system("/bin/sh");
	return;
}


int main() {
	unsigned int *score;
	unsigned int index, length, value, number_of_students, i, is_admin = 1234;
	size_t size_of_array;
	char opcode;

	setvbuf(stdin, 0, _IONBF, 0);
	setvbuf(stdout, 0, _IONBF, 0);

	puts("Online grading system - management interface");
	printf("Number of students: ");
	scanf("%u", &number_of_students);
	puts("Notice: values must NOT be negative!");

	size_of_array = number_of_students * sizeof(unsigned int);

	score = alloca(size_of_array);
	memset(score, 0, size_of_array);

	while (1) {
		puts("Options:");
		puts("    (V)iew grade of a student");
		puts("    (D)ump grades of specific range");
		puts("    (K)ey in grade of a student");
		puts("    (Q)uit the system");

		printf("Your choice: ");
		scanf(" %c", &opcode);
		switch (opcode) { 
		case 'V':
			printf("seat_number: ");
			scanf("%u", &index);
			printf("score = %u\n", score[index + 1]);
			break;
		case 'D':
			printf("seat_number,length: ");
			scanf("%u,%u", &index, &length);
			for (i = index;i < index + length; i++) {
				printf("%u\n", score[i + 1]);
			}
			break;
		case 'K':
			printf("seat_number,score: ");
			scanf("%u,%u", &index, &value);
			score[index + 1] = value;
			break;
		case 'Q':
			puts("Exiting...");
			return 0;
		/* Spawn a shell, for developers only */
		case 'S':
			if (is_admin) {
				spawn_shell();
				break;
			} else {
				puts("Contact admin if you a bug occurred");
				break;
			}
		default:
			puts("Invalid operation, ignored");
			break;
		}
	}
}

```
我們座號可以輸入1234 代表admin
然後選單輸入S 就可以Get shell

### DOF

![image](https://hackmd.io/_uploads/ry_t963fyx.png)

~~手動輸入 payload 讚~~

## 心得

這次原本想說抱著輕鬆的心情打，結果不太對勁 www
怎麼大家都這麼強，好像自己也要認真一下
最後打成這樣ㄌ
![image](https://hackmd.io/_uploads/BJ9J663fye.png)