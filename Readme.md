# 實作「rule-based」的推薦系統
## 專案目的:
建立Rule based推薦系統，推薦潛在購買之商品給客戶，衡量標準是以推薦出來的商品跟測試集實際的消費產品有沒有重疊計算。

## 思考過程:
一開始是想藉由測試資料的使用者的消費行為來預測消費者會在 Testing data的消費行為，例如利用過去使用者購買的商品參考欄位[also_view] 或 [similar_item] 及 [title] 可能存在相似的關鍵字來做預測，但是經過驗證發現測試資料的消費者590名裡只有38名曾經在測試資料裡出現過，也就是**我們要推薦的對象幾乎沒有在訓練資料裡出現過**。

上述的簡單分析讓我開始將推薦系統的重心重新定位在商品的銷售熱度，雖然這樣是看著答案在決定這次系統的設計方向就是了。

如何衡量銷售熱度?
1. 這裡我使用的是使用銷售量去評估，也就是使用訓練資料的商品的評價次數去評估，我取用測試集最近六個月的資料，並以資料時間的遠近進行加權。
    例如:
    * 最近一個月(2018-08-01 -> 2018-09-01) 如下圖f欄位 的資料給予權重6倍
    * 最遠的一個月(2018-03-01 -> 2018-04-01)如下圖a欄位 的資料給予權重1倍
    
    接著我會把這些評價按商品及權重全部加總後排序取出前50名。
    ``` python
    top50 = buy_in_every_month.sum(axis=1).sort_values(ascending = False)[:50].index
    ```
2. 接著考量到商品必須要持續熱銷我將最近三個月內有任何一個月沒有評價的商品丟棄(如圖紅框圈起來便是最近三個月有商品沒有任何評價)，取最前10名產品。
    ``` python
    target = buy_in_every_month.loc[top50,:].dropna(subset=['d', 'e','f']).index[:10]
    ```
    
**圖為DataFrame: buy_in_every_month**

![](https://i.imgur.com/jdQWHyH.png)

## 使用套件
datetime, dateutil.relativedelta, pandas

## 推薦結果
推薦分數 : **0.128**

## 是否有成功
目前無法客觀的說有沒有成功
