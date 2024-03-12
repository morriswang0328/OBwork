---
title: 'SPI to MCP25625'
disqus: hackmd
---

PIC16LF18345 SPI to MCP25625
===

## 目錄

[TOC]

## Modes of Operation 

說明
1. config 
2. normal
3. sleep
4. listen only
5. loopback

CAN Bit Timing
---
![](https://i.imgur.com/n3kNBLP.png)


==**參考版本設定紀錄**==
1. Fosc = 80MHz
2. Bit Timing = 250kbps
3. Time Quanta = 10TQ= 1(Sync)+1(Pro)+6(PS1)+2(PS2)
4. SJW = 1TQ

NBT = 1/250kbps = 4us = 10TQ
TQ = 400ns = 2.5MHz = 2*(BRP+1)* 1/40MHz(set@CANCKS)
所以 BRP = 7

因此沿用於MC25625時，只要設定BRP，NBT設定可以不變
TQ = 1/2.5MHz = 2 * (BRP+1) * 1/20MHz
設定BRP = 3

==(下) 有問題不可行的設定==

MC25625 對應設定
*CNF1 = 0000011 = 0x03
SJW[1:0]=0=1TQ +BRP[5:0]=3

*CNF2 = 10011000 = 0x98
BTLMODE=1
SAM=0 = The sample point is located between PHSEG1 and PHSEG2.
P1=3 = 4TQ, PRO=0 = 1TQ

*CNF3 = 0x01
SOF = 0(don't care) =  (CLKEN =0)
WAKFIL = 0
PS2 = 1 = 2TQ

CAN發INT的設定 CANINTE
---
![](https://i.imgur.com/1VMe2XZ.png)
![](https://i.imgur.com/izCSpkD.png)

當TX0 發送完成後會產生INT，用於通知控制MCU


Config mode
---
![](https://i.imgur.com/7tcw3np.jpg)

![](https://i.imgur.com/fFyyGsE.png)


```gherkin=
void DRV_CANSPI_ConfigurationSet(DRV_CANSPI_BIT_TIME_CONFIG *config)
{
    DRV_CANSPI_REG_CNF1 cnf1;
    DRV_CANSPI_REG_CNF2 cnf2;
    DRV_CANSPI_REG_CNF3 cnf3;

    cnf1.BRP = config->BRP;    // 0
    cnf1.SJW = config->SJW;    // 4TQ 

    cnf2.PropSeg = config->PropSeg;            // 7TQ
    cnf2.PhaseSeg1 = config->PhaseSeg1;        // 6TQ       
    cnf2.BusSampling = config->BusSampling;    // 1
    cnf2.BTLMode = config->PhaseSeg2Config;    // 

    cnf3.PhaseSeg2 = config->PhaseSeg2;
    cnf3.WakeUpFilterEnable = config->WakeUpFilterEnable;
    cnf3.SOF = config->SOFPinConfig;

    DRV_CANSPI_RegisterWrite(cREGADDR_CNF1, cnf1.byte);
    DRV_CANSPI_RegisterWrite(cREGADDR_CNF2, cnf2.byte);
    DRV_CANSPI_RegisterWrite(cREGADDR_CNF3, cnf3.byte);
}
```


程式流程
---

定時：檢查Flag
設定TX
發送TX
確定TX完成清除 TX0IF @ CANINTF


RX:
RX0接收後產生INT
控制MCU讀取RX0BUF
清除INT


---
###### tags: `PIC16LF18345` `周邊設定`
