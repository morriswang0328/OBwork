
nRF52 DK 使用 Segger Embedded Studio(ses) 進行開發 (Get Start) 
===
###### tags: `Nordic nRF52832`, `Segger Embedded Studio(ses)`

---

# 參考資料

* [<font size=5, color=black>使用SES開發的get start</font><br>](https://infocenter.nordicsemi.com/pdf/getting_started_ses.pdf)
* [<font size=5, color=black>開發者論壇</font><br>](https://devzone.nordicsemi.com/)
* [<font size=5, color=black>解釋softdevice 與SDK的關係</font><br>](https://www.cnblogs.com/iini/p/9095551.html)

---      

# Segger Embedded Studio(ses) 範例與使用

[<font size=5, color=blue> 請參考Youtube系列影片</font><br>](https://www.youtube.com/watch?v=YZouRE_Ol8g&list=PLx_tBuQ_KSqGHmzdEL2GWEOeix-S5rgTV)


# 無線Software Development Kit SoftDevice 

<font size=5, color=black>Board PCA10040</font><br>
![](https://i.imgur.com/ygeDnIw.png)
 
<font size=5, color=black>使用SoftDevice s140</font><br>
![](https://i.imgur.com/mufQQwp.png)

# 工具安裝

* [<font size=5, color=black>nRF52 DK 官網頁面</font><br>](https://www.nordicsemi.com/Software-and-Tools/Development-Kits/nRF52-DK)

* [<font size=5, color=black>下載 SDK 15.3.0</font><br>](https://www.nordicsemi.com/Software-and-tools/Software/nRF5-SDK/Download#infotabs)
![](https://i.imgur.com/qdWpd7f.png)

* [<font size=5, color=black>安裝ses</font><br>](https://www.segger.com/downloads/embedded-studio)

* [<font size=5, color=black>安裝 nRF-Command-Line-Tools</font><br>](https://www.nordicsemi.com/Software-and-Tools/Development-Tools/nRF-Command-Line-Tools/Download#infotabs)
![](https://i.imgur.com/uk1vTth.png)

# 功能初測：根據getting start流程

## 拖拉燒錄Hex檔
<font size=5, color=black>1. 先測試丟入hex,直接就可以燒了，自動重開機執行</font><br>

<font size=5, color=black>2. 測試燈號閃爍, Build & Run</font><br>

專案位置
![](https://i.imgur.com/4zyUi5K.png)

ses顯示
![](https://i.imgur.com/NOs5b8K.png)

執行
![](https://i.imgur.com/0Wmeidq.png)


## 範例：UART 傳輸

1. JLINK RTT VIEWR 設定
![](https://i.imgur.com/jo3BuTs.png)

2. Termite 3.4 設定完成
安裝這個才有包含plugin
![](https://i.imgur.com/6aUMjfe.png)
![](https://i.imgur.com/0DMHSpf.png)

3. Load SDK範例程式
![](https://i.imgur.com/P3L59Ld.png)

4. 按一次RESET就會丟一組字串
![](https://i.imgur.com/doEDsu9.png)


## 範例：測試Timer
1. Load solution
![](https://i.imgur.com/ooRJIO3.png)

<font size=5, color=red> 燈號有輪流閃爍 </font><br>




> 未來的你一定會感謝現在努力的自己. [name=]


> 破產是一種暫時的困境，貧困是一種思想的狀態. [name=Bill Gates]


:::info
**Find this document incomplete?** Leave a comment!
:::


