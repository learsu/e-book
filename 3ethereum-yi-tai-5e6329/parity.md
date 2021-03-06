# Parity

[https://www.parity.io/](https://www.gitbook.com/book/easonwang01/e/edit#)

Parity 為一個輕量級的節點，不用下載全部的區塊鏈資料到節點上，可以快速進行節點的同步，並且在啟動後會自動開啟一個前端網頁，可以查看目前節點狀態，同步後可以使用其 UI 介面發送交易與部屬、執行合約。

安裝：[https://github.com/paritytech/parity/releases](https://github.com/paritytech/parity/releases)

文件：[https://paritytech.github.io/wiki/](https://paritytech.github.io/wiki/)

![](/assets/螢幕快照 2018-01-30 下午10.26.00.png)

#### 路徑

安裝後的可執行程式通常會位於如下路徑：

```
Windows: C:\Program Files\Ethcore\Parity
macOS:  /Applications/Parity\ Ethereum.app/Contents/MacOS:$PATH
Linux: /usr/bin/parity
```

> 將以上路徑加入環境變數即可直接在任何Terminal輸入Parity執行。

區塊鏈資料與帳號金鑰通常會存在以下路徑：

```
Windows: %UserProfile%\AppData\Roaming\Parity
macOS: $HOME/Library/Application\ Support/io.parity.ethereum/
Linux: ~/.local/share/io.parity.ethereum/
```

#### 設定

有關詳細的設定說明可參考以下連結：

[https://github.com/paritytech/parity/wiki/Configuring-Parity](https://github.com/paritytech/parity/wiki/Configuring-Parity)

Parity可以使用一個`config.toml`檔案來寫入相關設定，可用以下網站來快速的產生一個設定檔：

[https://paritytech.github.io/parity-config-generator/](https://paritytech.github.io/parity-config-generator/。)

啟動 Parity 後，預設會讀取以下路徑下的設定檔：

```
Windows: %UserProfile%\AppData\Roaming\Parity\Ethereum\config.toml
macOS:  $HOME/Library/Application\ Support/io.parity.ethereum/config.toml
Linux: ~/.local/share/io.parity.ethereum/config.toml
```

也可以直接在啟動Parity時在CLI上加Flag：[https://github.com/paritytech/parity/wiki/Configuring-Parity\#cli-options](https://github.com/paritytech/parity/wiki/Configuring-Parity#cli-options)

#### 連線

方法1 :

如果我們有安裝Geth也可以使用Geth console連線到Parity

> 使用 geth attach ，後面加上Parity的 IPC 或 RPC 位置

```
geth attach $HOME/Library/Application\ Support/io.parity.ethereum/jsonrpc.ipc
```

方法2 :

安裝Web3

```
npm install web3
```

接著使用Node.js的command line：

```js
$ node
> Web3 = require("web3")
> web3 = new Web3(new Web3.providers.HttpProvider("http://localhost:8545"));
> web3.version

// 回傳相關資料
```

預設前往[http://127.0.0.1:8180/](http://127.0.0.1:8180/) 即可看到相關UI介面。

> 直接在Parity的網頁UI介面開啟Developer console，也可以使用Web3相關API

#### 改變連線Port

Parity 預設UI Port為8180，而節點Port為30303，JSON-RPC Port為8545，我們可以在設定檔內更改，或是直接輸入以下指令來指定Port。

```
parity --ports-shift 2  --datadir="./parity_dir02"
```

> `--ports-shift 2` 將會把（RPC, WebSockets, UI）等Port都自動往default的Port號碼往後加2，也可以改為其他數字。
>
> 記得要加上--datadir 自訂資料夾位置，否則會出現`db/LOCK: Resource temporarily unavailable` 錯誤。



