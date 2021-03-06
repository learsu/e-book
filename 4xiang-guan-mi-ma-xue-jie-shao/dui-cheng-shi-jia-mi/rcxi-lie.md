# RC系列

```
RC2 為64-bit區塊加密 (Block cipher) 在1987發表。

RC4 為目前最廣為使用的串流加密 (stream cipher)。

RC5 為 32/64/128-bit 區塊加密 (block cipher) 於 1994年開發。

RC6 為 128-bit 區塊加密 (block cipher) 由 RC5 衍生而來。
```

#### RC2

RC2  別名為 ARC2** 由 **[Ronald Rivest](https://en.wikipedia.org/wiki/Ron_Rivest)在1987年開發。 RC 意思為 Ron's Code 或是 Rivest Cipher。

一開始他的演算法是保持保密的，直到1996年RC2 才被匿名的貼到[sci.crypt](https://en.wikipedia.org/wiki/Sci.crypt)論壇上。

1998年 Ronald Rivest 才授權給[RFC](https://en.wikipedia.org/wiki/Request_for_Comments)公開 \( [RFC 2268](https://tools.ietf.org/html/2268)\) 。

RC2為64-bit 的區塊加密 \( block cipher \) ，但其現在已知容易遭受 [related-key attack](https://en.wikipedia.org/wiki/Related-key_attack)之攻擊 \( Kelsey et al., 1997 \)

#### RC4

RC4（ 來自Rivest Cipher 4的縮寫 ）是一種流加密算法 \( stream cipher \) ，一個字節一一地加解密，密鑰長度可變。

RC4是常見在WIFI加密中的WEP採用的加密算法，也曾經是TLS可採用的算法之一。

在1987年設計，由於RC4算法存在弱點，2015年2月所發布的 RFC 7465 規定禁止在TLS中使用RC4加密算法。

後來在2015年由RFC 7465禁止在所有版本的TLS中使用。 但由於不管是軟體還是硬體，實現RC4十分容易所以成為其廣泛使用的原因。

以下為Node.js範例

```js
const crypto = require('crypto');

const mode = 'rc4'; //可替換為rc4-40與rc4-hmac-md5

// 加密
const cipher = crypto.createCipher(mode, 'a password');
let encrypted = cipher.update('I_am_plaintext', 'utf8', 'hex');
encrypted += cipher.final('hex');
console.log(encrypted);

// 解密
const decipher = crypto.createDecipher(mode, 'a password');
let decrypted = decipher.update(encrypted, 'hex', 'utf8');
decrypted += decipher.final('utf8');
console.log(decrypted);
```

#### RC5

RC5 的區塊大小可以是32、 64 或是 128 bits 而 key 的大小可以是 \(0 到 2040 bits\)

實作方法可參考 : [http://www.engr.uconn.edu/~zshi/course/cse268f2004/rc5.pdf](http://www.engr.uconn.edu/~zshi/course/cse268f2004/rc5.pdf)

1.

```
1.產生子金鑰序列S = S[0],S[1], ...
2.使用兩個常數P, Q
3.S[0] = P
 下一個金鑰(S[n])是前一金鑰與Q相加取模數2 ** 32 (模數由區塊大小決定)
```

> P, Q定義如下

![](/assets/螢幕快照 2018-02-04 下午12.44.38.png)

其中Odd\( x \)代表與 x 最接近的奇數，而 w 可以是 16, 32 或 64。![](/assets/螢幕快照 2018-02-04 下午12.45.31.png)

2.加密

> r 代表回合數。

![](/assets/螢幕快照 2018-02-04 下午12.21.04.png)

3.解密

> r 代表回合數。

![](/assets/螢幕快照 2018-02-04 下午12.28.57.png)

#### RC6

RC6 其區塊大小為 128 bits 而其 key 的大小可為 128, 192, 256 bits 至 2040 bits。

RC6 非常類似於 RC5

實作方法可參考 : [https://people.csail.mit.edu/rivest/pubs/RRSY98.pdf](https://people.csail.mit.edu/rivest/pubs/RRSY98.pdf)

其中與RC類似，使用w/r/b表示

> The user supplies a key of b bytes, where 0 &lt; b &lt; 255. From this key, 2r + 4 words \(w bits each\) are derived and stored in the array `S[0, ..., 2r + 3]`This array is used in both encryption and decryption.

```
RC6-32/16/16 :

區塊大小64位元
16回合數
金鑰長度為128位元
```

加密：

![](/assets/螢幕快照 2018-02-04 下午12.57.52.png)

解密：

![](/assets/螢幕快照 2018-02-04 下午12.58.01.png)

