
Microchip dspic33 MCU 使用doxygen 進行程式註解
===
###### tags: `dsPIC33EP64MC503`,`doxygen`

:::success
2021.6.10 草稿
:::

---

[TOC]

---

# 參考資料
[設定參考1](https://www.itread01.com/content/1549180824.html)
[設定參考2](https://www.itread01.com/content/1546669275.html)
[註解編輯](https://b8807053.pixnet.net/blog/post/3612235)
[Complete Guide On Using Doxygen To Document C Source Code..!!](https://embeddedinventor.com/guide-to-configure-doxygen-to-document-c-source-code-for-beginners/)
[Special Commands](
https://www.doxygen.nl/manual/commands.html#cmdinclude)
[Doxygen 程式註解工具用於C語言](https://hackmd.io/@-C2FvrY8RnGVIGs9xyxOuQ/SJJvyvi5d)

---
# <font size=5, color=red>問題集</font>
若已新增的project無法在properties修改Encoding，可以在
**\nbproject\project.xml** 修改

![](https://i.imgur.com/wQtWumP.png)



---
# 安裝 MPLABX plugin

![](https://i.imgur.com/dsWLCvF.png)

# MPLABX 重新啟動後要求安裝
![](https://i.imgur.com/ZzXY3VV.png)

# 網址：http://sourceforge.net/projects/doxygen/files/
下載目前最新版本 1.9.1
![](https://i.imgur.com/yqN4rLZ.png)
![](https://i.imgur.com/SzIcZVk.png)


[<font size=5, color=blue>組態檔範例連結</font>](https://drive.google.com/file/d/1_-k8Un7wONF12hyq4dcth3JDlPhy-fJj/view?usp=sharing)

# 確定路徑有設置：執行檔、組態檔、說明文件輸出路徑
打勾build完成就會直接產生html檔
![](https://i.imgur.com/HfY4jsy.png)



# 為了正常顯示中文，需要修改Encode, 選擇專案右鍵的property
，如圖選擇Big5
![](https://i.imgur.com/Sd0ZbOI.png)

# 註解方法請參考
[<font size=5, color=blue>C語言範例</font>](https://drive.google.com/file/d/1IKHZTIvP-9YoXRgQch3dFHkRYrNWZrSW/view?usp=sharing)


# 資料夾內容
包含doxygen設定檔、專案說明檔.dox
![](https://i.imgur.com/zrFT8i9.png)


---
> 未來的你一定會感謝現在努力的自己. [name=]

:::info
**Find this document incomplete?** Leave a comment!
:::


