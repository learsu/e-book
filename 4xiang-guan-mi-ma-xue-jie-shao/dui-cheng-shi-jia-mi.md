# 對稱式加密 \( **Symmetric-Key Algorithm **\)

在加密和解密時使用相同的密鑰，常見的對稱式加密算法有：AES、3DES、IDEA、TwoFish、RC系列等等

而DES與BlowFish目前因安全性已不建議採用。

而下面展示一個對稱式加密最基本的原理，假設我們要用以下密鑰 `11110011` 將密文 `Test` 加密：

```
密文: Test
密鑰: 11110011
```

我們先把密文轉換為ACSII 二進位

```js
function textToBinary(string) {
  return string.split('').map(function (char) {
    return '0' + char.charCodeAt(0).toString(2);
  })
}

textToBinary("Test");
// ["01010100", "01100101", "01110011", "01110100"]
```

上面每個陣列對應一個英文字母，我們把每個陣列元素與密鑰分別做 XOR 運算\(註1\):

```js
function XOR(a, b) {
 if(a.length !== b.length) {
   console.log("要求兩者二進位數字長度相同");
   return
 }
 let num = (parseInt(a, 2) ^ parseInt(b, 2)).toString(2);
 if(num.length !== a.length) {  // 因前面為0會被省略，所以要手動補0
    let padLength = a.length - num.length;
    num = "0".repeat(padLength) + num;
  };
  return num 
}

let key_ = "11110011"; // 密鑰

// 加密
(["01010100", "01100101", "01110011", "01110100"]).map(d => XOR(d, key_)); // 使用密鑰對每個元素做XOR

//加密後結果:  ["10100111", "10010110", "10000000", "10000111"]
```

然後進行解密

```js
//解密 (等於是把加密後的結果在與KEY做一次XOR)
(["10100111", "10010110", "10000000", "10000111"]).map(d => XOR(d, key_));

//解密後結果 ["01010100", "01100101", "01110011", "01110100"]

// 最後轉為字串
(["01010100", "01100101", "01110011", "01110100"]).map(d => String.fromCharCode(parseInt(d, 2))).join('');
// 得到字串:Test
```

# Stream Ciphers 與 Block Ciphers

對稱式加密可以使用Stream Ciphers或是Block Ciphers將明文預先進行分段後再進行演算法加密來提高安全性，以及對於不同加密需求時可以使用不同模式 \( 例如當明文長度大於 AES 的 128 bits 限制時，可以用區塊加密將其切割 \)。

> 可以在Node.js使用  crypto.getCiphers\(\) 來查看可用之加密方法。

#### 1.Stream Ciphers

Stream Ciphers \( 資料流加密 \) 並不會將明文切分為區段，而是一次加密一個資料流的位元或是位元組。常見的作法是將 較短的加密鑰匙延展成為無限長、近似亂碼的一長串金鑰串流 \( keystream \)，再將金鑰串流和原始資料 （ plaintext ）經過XOR運算後，產生密文資料 （ cipher text ）。

Stream Ciphers 被用在視訊串流或行動通訊等領域。使用Stream Cipher 之加密演算法例如： A5/1、RC4

由於它可以用硬體來實作，在以前CPU運作速度不夠快的時代比較流行。

```
1.金鑰長度無法和串流明文一樣長
2.金鑰產生金鑰串流
3.金鑰串流不能重複
```

#### 2.Block Ciphers

Block Ciphers \( 區段加密 \) 會將明文分成數個n個字元或位元的區段，並且對每一個區段資料使用相同的演算法和密鑰加密。

一開始會把明文先拆開成區塊，然後第一個區塊先進行加密演算法後得到密文，之後這個密文再繼續跟後續區塊做計算。

假設M為明文，其將分割成M1、M2… Mn區段， 然後K為密鑰，其可表示為 : E\(M,K\)=E\(M1,K\).E\(M2,K\)…..E\(Mn,K\)

較適合對已知加密檔案內容的檔案進行加密。

> 參考至:[https://security.stackexchange.com/a/345](https://security.stackexchange.com/a/345)

以下先介紹比較早期發展的四種區塊加密方式：ECB、CBC、CFB和OFB

#### ECB

最簡單的加密模式即為（Electronic codebook，ECB）模式。需要加密的訊息按照設定的大小被分為數個區塊，並對每個區塊進行獨立加密。但缺點在於同樣的明文區塊會被加密成相同的密文區塊，並且重複使用同一把金鑰，因此並不推薦用於現代的加密協定中。

> ![](/assets/1202px-ECB_encryption.svg.png)
>
> [https://en.wikipedia.org/wiki/Block\_cipher\_mode\_of\_operation](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation)

過程主要為

```
將要加密的明文 ( plaintext ) 分為多個區塊，並用同一支金鑰進行加密的動作。
```

#### CBC

全名為Cipher-block chaining，為區塊加密中最常用的模式，在CBC模式中，每個明文區塊先與前一個密文區塊進行XOR後，再進行加密，也就是將前一個區塊加密後的結果作為加密第二個區塊的輸入。在這種方法中，每個密文塊都依賴於它前面的所有明文區塊。並且，其會要求輸入一個初始向量，該初始向量會先跟第一個明文區塊做XOR。

主要缺點在於加密過程是依序進行的，無法同時進行，所以速度較慢，並且加密訊息必須被填充到區塊大小的整數倍，但其解密是可以並行運算的。

> 其加密過程如下圖
>
> ![](/assets/1202px-CBC_encryption.svg.png)
>
> [https://en.wikipedia.org/wiki/Block\_cipher\_mode\_of\_operation](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation)

```
1. 初始向量 IV (註2) 與 Plaintext 做 XOR 之後再和金鑰進行演算法加密
2. 然後第上一步驟的結果與下一個區塊的 Plaintext 做 XOR 之後再和金鑰進行演算法加密
3. 重複上一步驟直到所有內容加密完成
```

#### CFB

類似剛才提到的CBC模式，前一部分的明文改變將會影響到接下來加密出的密文，其將區塊加密變為可自行同步的串流加密法，如果部分的cipher text在傳輸過程遺失，也不會導致所有原始訊息遺失，仍然可以繼續解密出剩下的訊息，且訊息無需進行填充到一定長度，

其加密過程如下圖

![](/assets/1202px-CFB_encryption.svg.png)

> [https://en.wikipedia.org/wiki/Block\_cipher\_mode\_of\_operation](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher_Feedback_%28CFB\)

過程大致如下

```txt
1. IV 直接加入金鑰並進行演算法加密
2. 將第一步驟的結果與 plain text 進行 XOR 的運算
3. 將第二個步驟所產生的結果作為下一回合的輸入
4. 重複上一步驟直到所有內容加密完成
```

#### OFB

全名為（Output feedback， OFB）可以將區塊加密變成同步的串流加密法。IV與Key產生出block cipher encryption後與明文進行XOR，得到密文。與其它串流加密法一樣，密文中一個 bit 的翻轉會使明文中同樣位置的bit 也產生翻轉。這種特性使得許多[錯誤校正碼](http://www.wikiwand.com/zh-mo/前向錯誤更正)，例如[奇偶校驗位](http://www.wikiwand.com/zh-mo/奇偶校验位)，在加密前計算與加密後進行校驗均可得出正確結果。

由於XOR操作的對稱性，加密和解密操作是完全相同的：

> ![](/assets/1202px-OFB_encryption.svg.png)[https://en.wikipedia.org/wiki/Block\_cipher\_mode\_of\_operation](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation)

```
1. IV 直接加入金鑰並進行演算法加密
2. 將第一步驟的結果與 plain text 進行 XOR 的運算 
3. 將第一步驟所產生的結果作為下一回合的輸入
4. 重複上一步驟直到所有內容加密完成
```

#### CTR

稱為Counter mode，CTR模式也稱為計數器模式，每個區塊對應一個累加的計數器，通過計數器來生成加密密鑰串流，計數器可以是任意保證長時間不產生重複輸出的函數。下圖的Nonce加上Counter當做一個改變的數值，Nonce和前面幾種模式的IV類似，每次加密都需要隨機生成。而計數器Counter是累加的。CTR模式特點是每組加密都是獨立的，不依賴前一組，所以可以進行並行計算。

在計數器模式下，我們先對一個累加的計數器進行加密，再用加密後的bits序列與明文分組進行 XOR 得到密文。

> ![](/assets/1202px-CTR_encryption_2.svg.png)[https://en.wikipedia.org/wiki/Block\_cipher\_mode\_of\_operation](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation)

另外有兩種區塊加密法與CTR相關

```
1. GCM: GCM中的G就是指GMAC，C就是指上述的CTR。
2. CCM: CCM為CMAC認證算法結合CTR。
```

而四種方式的比較與選擇可參考：[https://stackoverflow.com/a/1220869](https://stackoverflow.com/a/1220869)

# Node.js中的對稱式加密相關函式

主要包含

```
crypto.createCipher()
crypto.createCipheriv()
```

其中createCipher是輸入密碼，而createCipheriv需輸入key與iv值

> 例如cbc等需要iv的也可以用createCipher並輸入密碼，因為它會自動衍生出iv與key參考下面程式
>
> [https://gist.github.com/bnoordhuis/2de2766d3d3a47ebe41aaaec7e8b14df](https://gist.github.com/bnoordhuis/2de2766d3d3a47ebe41aaaec7e8b14df)

---

#### 註1: XOR

\( 兩兩數值相同為否，而數值不同時為真 \): 例如1 xor 1為 0 ， 1 xor 0 為 1

F\(false\)亦代表0，T\(true\)亦代表1

![](/assets/2333.png)

至於為何要使用XOR在對稱式加密，不使用AND或OR，可參考:[https://stackoverflow.com/a/24731845](https://stackoverflow.com/a/24731845)

#### 註2: IV 初始向量

通常是一個亂數，讓攻擊者難以對同一把金鑰產生的密文進行破解，會加入區塊加密中一起進行加密運算。

[https://zh.wikipedia.org/wiki/初始向量](https://zh.wikipedia.org/wiki/初始向量)

