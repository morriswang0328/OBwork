
MPLABX gitignore研究
===
###### tags: `MPLABX`

:::info

使用MPLABX v5.50

:::
---

[TOC]

---

# 參考資料
[設定參考1](https://www.itread01.com/content/1549180824.html)
<font size=5, color=black>藉由在程式內編寫固定格式的註解，自動產生一份中文的說明文件，方便解讀、回憶、交接。</font><br>

---

# Package
package會移除toolchain訊息，因此要重新設定
![](https://i.imgur.com/Me2JFuU.png)

# Build
.generated_files 會在MPLABX讀取專案時產生
![](https://i.imgur.com/PTZu4GT.png)

Build之後新增：/build, /dist

![](https://i.imgur.com/MNLtOiC.png)

![](https://i.imgur.com/FjBCFxg.png)


# Clean
將Build直接Copy到Clean，兩個資料夾完全相同

使用 MPLABX 讀入檔案夾後會產不同的檔案：
* generated_files/flags 部分會不同
* nbproject/
    Makefile-default.mk
    Makefile-genesis.properties

![](https://i.imgur.com/vgbG2rP.png)

進行Build後增加/build, /dist 
![](https://i.imgur.com/zmwtF8Z.png)

Clean: /build, /dist 都清掉了
![](https://i.imgur.com/e1AqEpG.png)

# Gitignore新增：對應新版v5.50
![](https://i.imgur.com/4MNCwjd.png)

gitignore_global_default.txt

```c=
#ignore MPLAB IDEX
*.d
*.pre
*.p1
*.lst
*.sym
*.obj
*.o
*.sdb
*.obj.dmp
.generated_files/
.idea/
nbproject/private/
nbproject/Package-*.bash
build/
nbuild/
dist/
nbdist/
nbactions.xml
nb-configuration.xml
funclist
nbproject/Makefile-*
disassembly/
*.map
*.zip

```

---

> 未來的你一定會感謝現在努力的自己. [name=]

:::info
**Find this document incomplete?** Leave a comment!
:::


