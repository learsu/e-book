# Regtest Mode

有時開發者需要進行比特幣區塊鏈測試，這時Regtest Mode可以提供一個從零開始的區塊鏈環境，並且是不用和網路其他節點互動的私有鏈。

### Bitcoin-qt執行Regtest Mode

我們可以輸入以下指令，讓Bitcoin-qt執行Regtest Mode

```
./bitcoin-qt -regtest
```

Bitcoin-qt 執行regtest Mode:![](/assets/螢幕快照 2017-12-26 下午3.53.17.png)

## 使用Bitcoin-cli

我們也可以使用Bitcoin-cli 來設定我們想要的環境

1. 先執行Bitcoin server

```
./bitcoind -regtest -daemon
```

2.產生500個區塊

```
bitcoin-cli -regtest generate 500
```

![](/assets/螢幕快照 2017-12-26 下午4.16.37.png)3.查看餘額

```
bitcoin-cli -regtest getbalance
```

4.查看可用指令

```
./bitcoin-cli -regtest help
```

1. 取得節點資訊

```
./bitcoin-cli -regtest getblockchaininfo
```

![](/assets/螢幕快照 2017-12-26 下午4.25.28.png)

## 最後記得關閉Bitcoin server

```
./bitcoin-cli -regtest stop
```

---

### 重置Regtest chain

將先前章節所提到的Bitcoin資料夾路徑下的 regtest 資料夾刪除即可
