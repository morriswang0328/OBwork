
dsPIC33 Clock System
===
###### tags: `Microchip dsPIC33EP64MC503`

:::success
本文以框架方式撰寫便於未來搜尋，參考 [OSC Datasheet](https://ww1.microchip.com/downloads/en/DeviceDoc/70005131a.pdf)
:::

---

## 方塊圖

----

![](https://i.imgur.com/jmfjBh8.png)

----

* POSC = OSC1 + OSC2 + PLL(optional)
* SOSC = SOSCI + SOSCO 
* LPRC = Internal Low Power 
* FRC = Internal Fast + PLL (opional)
* AUXILIARY OSCILLATOR for USB

---

### POSC (primary OSC)
#### 模式
* XT(Medium Speed) = 3.5~10 Mhz
* HS(High Speed) = 10~40 MHz
* EC(Extenal Clock Source operation) = 0~60 Mhz

---

### FRC (Internal Fast RC) Osc 7.37Hz
#### 用途
* 開機
* FSCM故障觸發時切換到此OSC

---

### SOSC (Secondary) Osc 32.768kHz for Low Power

---

### LPRC (Low Power RC) Osc 32kHz
用於不注重精準注重功耗的應用：
* Power-Up Timer  
  ==屬於RESET功能，控制PowerUP完成後OSC再動作==

* Watchdog Timer  ==看門狗==
* Fail-Safe Clock Monitor circuits (FSCM)

----

## FSCM (Fail-Safe Clock Monitor)
功能：FSCM會每2~4ms持續監測Sys OSC,如果沒有訊號會將Sys OSC切到 FRC
,程式可以設定Reset Sys OSC或是Shut down

--> LPRC的功能不會被影響：WDT

---

### AUXILIARY OSCILLATOR for USB

---

### Clock Switching 時脈切換

* Two-Speed Start-up sequence： FRC開機 --> 指定OSC
* FSCM:  指定OSC-->FRC 
* 程式指定切換：進入sleep or doze mode

---

> 未來的你一定會感謝現在努力的自己. [name=]


