
Git 使用規範
===
###### tags: `Git`

:::success
2021.06.10 草稿
:::
---

# 參考資料
[三種分支方法介紹](http://www.ruanyifeng.com/blog/2015/12/git-workflow.html)
[Commit 寫法格式參考](https://wadehuanglearning.blogspot.com/2019/05/cit-commit-commit-why-what-commit.html)

---
# 使用空間
使用公司EMAIL在Github開設新帳號

# 使用工具
使用Sourcetree作為Git管理工具

# 分支規則
參考git flow, github flow, gitlab flow三種方法，以目前的程式的規模，使用最簡單原則：

* master: 開發主線，進版都會在這條線上
* branch：根據下述情形開分支:
    1. 新功能分支名稱範例: 
    <font size=4, color=blue>Fea－增加輪速判斷 </font><br>
    
    2. 解bug分支名稱範例: 
    <font size=4, color=blue>Bug－車輛停止時機誤判</font><br>
    
    3. 特殊版本分支名稱範例：
    <font size=4, color=blue>Ex－耐久測試用</font><br>
    
     情形1&2完成後merge to master，情形3分支永遠存在
 
 * release: 釋出給客戶測試版本，利用tag註明編號
 ex. 2021061501

# 何時要使用tag
* release的版本
* EMC送測時FW
* 耐久測試用定版

# Commit Message 寫法格式

```
[Type]修改說明
 * 不要超過 50 個字元，結尾不要加句號，為必要欄位。

問題描述：
 * 具體說明問題發生的情形與現象
 結尾不要加句號，為必要欄位

發生原因：
 * 說明程式碼變動的項目與原因，還有與先前行為的對比。
 * 結尾不要加句號，為必要欄位

解決方法：
 * 簡述解決方法，並說明如何驗證
 * 複雜說明請以hackmd文件說明並放上連結
 * 結尾不要加句號，為必要欄位
```

## Type說明: 只允許使用以下類別

Type 是用來告訴進行 Code Review 的人應該以什麼態度來檢視 Commit 內容。

例如：看到 Type 為 fix，進行 Code Review 的人就可以用「觀察 Commit 如何解決錯誤」的角度來閱讀程式碼。

若是 refactor，則可以放輕鬆閱讀程式碼如何被重構，因為重構的本質是不會影響既有的功能。

利用不同的 Type 來決定進行 Code Review 檢視的角度，可以提升 Code Review 的速度。因此開發團隊應該要對這些 Type 的使用時機有一致的認同。

項目：
* init: 第一次commit
* feature: 新增/修改功能(對應需求修改，修改已上傳commit的內容)
* fix: 修正錯誤
* docs: 新增/修改註解，文件，設定檔等
* enable/disable: 開啟/關閉某個功能
* style: 格式修改(不影響程式碼運行的變動 white-space, formatting, missing semi colons等)
* refactor: 重構(既不是新增功能也不是修補 bug 的程式碼變動)以增加可讀性與維護便利性
* perf: 改善效能，刪除冗餘等
* revert: 因故撤銷先前的 commit 的功能
* remove: 刪除檔案(若為新增檔案請使用feature)
* upgrade: 開發環境更新(SDK, MCC等)


## 範例

[fix] 自訂表單新增/編輯頁面，修正離開頁面提醒邏輯
問題：
1. 原程式碼進入新增頁面後，沒做任何動作之下，離開頁面會跳提醒
2. 原程式碼從新增/編輯頁面回到上一頁後（表單列表頁面），離開頁面會跳提醒
原因：
1. 新增頁面時，頁面自動建立空白題組會調用 sort_item，造成初始化 unload 事件處理器。
2. 回到上一頁後，就不需要監聽 unload 事件，應該把 unload 事件取消。
調整項目：
1. 初始化 unload 事件處理器：排除新增表單時，頁面自動建立空白題組調用 sort_item 的情境
2. 回到上一頁後，復原表單被異動狀態且清除 unload 事件處理器


# 上傳前需確認

1. build必須成功
2. 修改過的功能必須驗證完成，複雜的問題需規劃測試方法進行測試驗證
3. 每次上傳不超過三個Type，依據修改的重要性順序填寫


---
> 未來的你一定會感謝現在努力的自己. [name=]

:::info
**Find this document incomplete?** Leave a comment!
:::


