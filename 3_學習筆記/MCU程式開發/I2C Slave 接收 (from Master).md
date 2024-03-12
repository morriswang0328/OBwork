---
title: 'I2C Slave 接收 (from Master)'
disqus: hackmd
---

I2C Slave 接收 (from Master)
===
![downloads](https://img.shields.io/github/downloads/atom/atom/total.svg)
![build](https://img.shields.io/appveyor/ci/:user/:repo.svg)
![chat](https://img.shields.io/discord/:serverId.svg)

<style>
.blue {  color: blue; }
.red  {  color: red;  }
</style>


[TOC]
## 已知問題/現狀說明
#### 
- [x] Master 來源不穩定，待解決後再繼續確認
- [x] I2C Slave 傳送 (to Master) 未完成


## I2C Slave 接收 p.323
 
#### 
- [x] i2c slave mode operation p.327
- [x] 設定 7-bit Addressing Mode

![](https://i.imgur.com/xjISFNf.png)
![](https://i.imgur.com/nme3Jyb.png)


Slave Reception Regs
---

![](https://i.imgur.com/W6qUwHG.png)
![](https://i.imgur.com/ajlxeSf.png)
![](https://i.imgur.com/zpUvx38.png)
![](https://i.imgur.com/LH9GmRQ.png)
![](https://i.imgur.com/LczhJLV.png)


### AHEN? DHEN? SEN?   p.359


![](https://i.imgur.com/5jaAByY.png)

![](https://i.imgur.com/hZGY2MT.png)
![](https://i.imgur.com/KIpwLrt.png)

![](https://i.imgur.com/AyjWHDC.png)
![](https://i.imgur.com/AIpJlir.png)


#### 
- [x] 設定  <span class="blue">SSP1CON1</span> = 0
- [x] 設定 <span class="red">SSP1CON2</span> = 6
- [x] 設定 <span class="blue">SSP1CON3</span> = 0

Slave 接收流程圖
---

```sequence
Master->Slave: S detect, SSPxIF =1 
Note right of Slave: SDA Low
Slave-->Master: SSPxIF =1
Note left of Master: responds
Master->Slave: Send address

```

Slave Trasmission p.333
---
![](https://i.imgur.com/Dl1xnb8.png)
![](https://i.imgur.com/2nh3mHN.png)


User flows
---
```sequence
Alice->Bob: Hello Bob, how are you?
Note right of Bob: Bob thinks
Bob-->Alice: I am good thanks!
Note left of Alice: Alice responds
Alice->Bob: Where have you been?
```

> Read more about sequence-diagrams here: http://bramp.github.io/js-sequence-diagrams/

Project Timeline
---
```mermaid
gantt
    title A Gantt Diagram

    section Section
    A task           :a1, 2014-01-01, 30d
    Another task     :after a1  , 20d
    section Another
    Task in sec      :2014-01-12  , 12d
    anther task      : 24d
```

> Read more about mermaid here: http://mermaid-js.github.io/mermaid/

## Appendix and FAQ

:::info
** 有問題請留言!!
:::

###### tags: `PIC16LF18345` `周邊設定`
