
INA293 EVM study
===
###### tags: `INA293`

:::info
:::
---

[TOC]

---

# 參考資料
[INA293 Datasheet](https://www.ti.com/lit/ds/symlink/ina293.pdf?ts=1628494159223&ref_url=https%253A%252F%252Fkr.mouser.com%252F)
[INA293 Product page](https://www.ti.com/product/INA293)
[INA293 EVM](https://www.ti.com/tool/INA293EVM)
[shunt Resistor 2512](https://www.mouser.tw/Passive-Components/Resistors/Film-Resistors/Thick-Film-Resistors-SMD/_/N-7gz42?P=1yzc7n1)

---
# IC 
說明：在負載路徑上放一個Rsense，偵測Rsense的的電壓並放大成為輸出(以A1為例，放大20倍)

![](https://i.imgur.com/9xqnuHX.png)
![](https://i.imgur.com/Byb06tf.png)


# EVM

規劃：連接36V, 水泥電阻1k Ohm作為負載，新增Rsense 1ohm，若電流為100mA，Vsense=0.1V，因此Vout=2V

<font size=4, color=blue>
MCU：將Vout接到ADC，GND對接，可以確認讀取電流
</font><br>

![](https://i.imgur.com/T55TpJh.jpg)

DNP是英文（Do Not Populate）的意思，譯爲“不焊接”。
實際使用過程中，在PCB上會留有空焊盤，可以做測試引腳用，或者在信號質量不好時，加焊下拉電阻，調整信號質量。
![](https://i.imgur.com/XokjVcP.png)


## Setup
1. 根據Gain選擇板子
2. VS連接 1.7~20V，GND 接地
3. Vin+ = J1_A1_pin1, Vin-=J1_A1_pin2


---

> 未來的你一定會感謝現在努力的自己. [name=]

:::info
**Find this document incomplete?** Leave a comment!
:::


