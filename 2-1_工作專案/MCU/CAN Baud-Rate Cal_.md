
CAN Baud-Rate Cal.
===
###### tags: `Microchip`

:::info
:::
---

[TOC]

---

# 參考資料
[設定參考1](https://www.itread01.com/content/1549180824.html)
<font size=5, color=black>藉由在程式內編寫固定格式的註解，自動產生一份中文的說明文件，方便解讀、回憶、交接。</font><br>

---

# 範例
![](https://i.imgur.com/rPebpeu.png)

![](https://i.imgur.com/GfB8j7w.png)


Step1: 
N=8~25
![](https://i.imgur.com/AEl8zLC.png)

F_baud_rate = 250kbps
FTQ=16*250kbps = 40Mbps


FCAN=80Mhz
![](https://i.imgur.com/FiCLiFn.png)
![](https://i.imgur.com/rQ0GPc3.png)
BRP = 80M/(2*25M)-1=159

![](https://i.imgur.com/VyGiUPR.png)


# MCC設定
![](https://i.imgur.com/fs5eJeM.png)

C1CFG1 = 0x07 = b00000000 (00)(001110)
SJW=1TQ, BRP=7=TQ=2X7X1/FCAN

C1CFG2 = 0x1A8 = b0(0)000(001) (1)(0)(101)(000)
WAKFIL=0
Phase Segment 2 = 1 = 2TQ
Phase Segment 2 Time = free programmable
SAM=sample once
Phase Segment 1=101=6TQ
Propagation Time Segment=1TQ

CAN bit Time = 1+1+6+2


# Kvaser CANKing setting

![](https://i.imgur.com/AT5EGHJ.png)
SJW = Sync Seg 

![](https://i.imgur.com/7EF9FmJ.png)
![](https://i.imgur.com/oHqFHOi.png)

Tseg1 = propregation seg + phase seg


---
# CAN時序錯誤分析 2021.8.17

## 發現
固定時間T1=100us送出CAN，Kvaser讀取異常出現Error frame

## 環境
MPLABX v5.50
![](https://i.imgur.com/hKgPOzV.png)

## 修改CAN設定
![](https://i.imgur.com/XykEmBl.png)

## 修改Kvaser設定
![](https://i.imgur.com/oHFAcjV.png)

---

> 未來的你一定會感謝現在努力的自己. [name=]

:::info
**Find this document incomplete?** Leave a comment!
:::


