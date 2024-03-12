Motec 使用說明
===
###### tags: `Motec`

:::info
:::
---

[TOC]

---

# 參考資料

**:question:[產品頁面][id]** 

[id]: https://www.motec.com.au/c125/c125overview/ "主頁"

**使用說明**
{%youtube f1FH0n4qz9s %}

---

# Pin腳圖
![背面接頭](https://i.imgur.com/Zxkj1V7.png =400x)![接線編號](https://i.imgur.com/5t80Rzx.png =300x)

# 散線
---
## 電源: pin33=B+=紅，pin34=B-=黑
![](https://i.imgur.com/IQZczpZ.png  =400x)


## CAN2: pin26=CANL=綠,pin27=CANH=白
![](https://i.imgur.com/O7T0T8p.png  =400x)
DB9-2=綠、DB9-7=白
![](https://i.imgur.com/OkpY3gj.png =400x)


## I/O: 灰色
![](https://i.imgur.com/rK8oCEK.png)
![](https://i.imgur.com/3Z7K0pl.png)

## Ethernet=連接PC編輯

![](https://i.imgur.com/KPqKq9K.png =400x)
![](https://i.imgur.com/RQ1AHMM.png =400x)

---

# 束線

---
## ADC: 藍、綠、橘、黃
![](https://i.imgur.com/EJejEUw.png =400x)


## 5V輸出:紅(+), 黑(-)
![](https://i.imgur.com/42DJvs5.png =500x)


## AT1&2:棕、紫 
![](https://i.imgur.com/iKyMpaS.png =400x)


## SPD: 白、灰
![](https://i.imgur.com/tkis59e.png =400x)
![](https://i.imgur.com/GvMiZx5.png =400x)


# 接線
X=Check
- [X] 開始記錄 = 14,9
- [X] 換頁= 15,9
- [ ] SPD= 22,7(8V) + 200 Ohm 電阻
      使用ATS685 14mA & 6mA -> 2.8V & 1.2V
- [ ] GEM油壓 = 6(AV4) ,8(5V),9(0V)

* 12V分接一條線給輪速用

## 紀錄開關
pin14 + 開關 + pin9
- [ ] 開始記錄 Button (左右開關式)
- [ ] pin9 1對5(紀錄、換頁、SPD、CAN、油壓)

## 換頁按鈕
pin15+ 按鈕 + pin9
- [ ] 按鈕

## SPD = ATS685
VCC=電源12V=電源紅 
GND=Sensor輸出接200 Ohm 1W 
R前：接到pin22=線束白
R後：接地pin9=線束黑

- [ ] 2pin母座線
- [ ] 12V分接線
- [ ] 200 ohm數顆
- [ ] R前線 20mA AWG24硬線
- [ ] R後線 20mA AWG24

## 電源=接行動電源12V
12V = pin33 黑束紅
GND = pin34 黑束黑
- [ ] 12V(兩線t o圓頭, 0.5A) (帶去試)
- [ ] pin34 GND1對2 (V_GND, SPD_GND)
- [ ] 買硬質線

## 油壓
5V電源 = pin8 線束紅
GND = pin9 線束黑
訊號 = pin6 線束黃 
- [ ] 油壓白色公頭數個 (帶去試)
- [ ] 紅黑白三線接MOTEC

## CAN 
CANH = pin27 白
CANL = pin26 綠
- [ ] CAN 公頭數個 (廣華 SM-2.5可用？)

# 待確認
* 紀錄時間 --> 重開機才會檢查 非即時更新
* 消耗容量


# 畫面設計
GPS SPEED         DASH TEMP
FWS SPEED

# 項目


---

> 未來的你一定會感謝現在努力的自己. [name=]

:::info
**Find this document incomplete?** Leave a comment!
:::


