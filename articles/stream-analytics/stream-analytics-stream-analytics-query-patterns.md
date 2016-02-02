<properties
    pageTitle="串流分析中一般使用模式的查詢範例 | Microsoft Azure"
    description="常見的 Azure 串流分析查詢模式 "
    keywords="query examples"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="12/04/2015"
    ms.author="jeffstok"/>



# 一般串流分析使用模式的查詢範例

## 簡介

在 Azure 資料流分析查詢以類似 SQL 的查詢語言，而其已記錄 [這裡](https://msdn.microsoft.com/library/azure/dn834998.aspx)。 本文根據真實世界案例概述幾個常見查詢模式的解決方案。 這是進行中的工作，並將繼續不間斷使用新模式進行更新。

## 查詢範例：資料類型轉換

**描述**: 輸入資料流上定義屬性的型別。
例如汽車權數即將輸入的資料流為字串，必須轉換為 INT 以執行總結。

**輸入**：

| 請確定| 時間| 重量|
| --- | --- | --- |
| Honda| 2015-01-01T00:00:01.0000000Z| "1000"|
| Honda| 2015-01-01T00:00:02.0000000Z| "2000"|

**輸出**：

| 請確定| 重量|
| --- | --- |
| Honda| 3000|

**解決方案**：

    SELECT
        Make,
        SUM(CAST(Weight AS BIGINT)) AS Weight
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**說明**:
使用 CAST 陳述式的權數欄位指定其型別 (請參閱 < 支援的資料型別的清單 [這裡](https://msdn.microsoft.com/library/azure/dn835065.aspx))。

## 查詢範例：使用 Like/Not like 進行模式比對

**描述**: 檢查事件的欄位值是否符合特定模式
例如傳回以 A 開頭和以 9 結尾的車牌號碼

**輸入**：

| 請確定| LicensePlate| 時間|
| --- | --- | --- |
| Honda| ABC-123| 2015-01-01T00:00:01.0000000Z|
| Toyota| AAA-999| 2015-01-01T00:00:02.0000000Z|
| Nissan| ABC-369| 2015-01-01T00:00:03.0000000Z|

**輸出**：

| 請確定| LicensePlate| 時間|
| --- | --- | --- |
| Toyota| AAA-999| 2015-01-01T00:00:02.0000000Z|
| Nissan| ABC-369| 2015-01-01T00:00:03.0000000Z|

**解決方案**：

    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LicensePlate LIKE 'A%9'

**說明**:
使用 LIKE 陳述式檢查 LicensePlate 欄位值以 A 開頭，則任何零或多個字元的字串和以 9 結尾。

## 查詢範例：為不同的案例/值指定邏輯 (CASE 陳述式)

**描述**: 提供不同的計算欄位，根據一些準則。
例如: 提供的字串描述多少汽車通過的相同，所作的特殊案例 1。

**輸入**：

| 請確定| 時間|
| --- | --- |
| Honda| 2015-01-01T00:00:01.0000000Z|
| Toyota| 2015-01-01T00:00:02.0000000Z|
| Toyota| 2015-01-01T00:00:03.0000000Z|

**輸出**：

| CarsPassed| 時間|
| --- | --- | --- |
| 1 Honda| 2015-01-01T00:00:10.0000000Z|
| 2 Toyotas| 2015-01-01T00:00:10.0000000Z|

**解決方案**：

    SELECT
        CASE
            WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
            ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
        END AS CarsPassed,
        System.TimeStamp AS Time
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**說明**:
CASE 子句可讓我們提供根據一些條件 (在本例中的彙總視窗中，汽車計數) 不同的運算。

## 查詢範例：將資料傳送至多個輸出

**描述**: 將資料從單一工作傳送到多個輸出目標。
例如分析臨界值為基礎的警示資料和封存至 blob 儲存體的所有事件

**輸入**：

| 請確定| 時間|
| --- | --- |
| Honda| 2015-01-01T00:00:01.0000000Z|
| Honda| 2015-01-01T00:00:02.0000000Z|
| Toyota| 2015-01-01T00:00:01.0000000Z|
| Toyota| 2015-01-01T00:00:02.0000000Z|
| Toyota| 2015-01-01T00:00:03.0000000Z|

**Output1**：

| 請確定| 時間|
| --- | --- |
| Honda| 2015-01-01T00:00:01.0000000Z|
| Honda| 2015-01-01T00:00:02.0000000Z|
| Toyota| 2015-01-01T00:00:01.0000000Z|
| Toyota| 2015-01-01T00:00:02.0000000Z|
| Toyota| 2015-01-01T00:00:03.0000000Z|

**Output2**：

| 請確定| 時間| 計數|
| --- | --- | --- |
| Toyota| 2015-01-01T00:00:10.0000000Z| 3|

**解決方案**：

    SELECT
        *
    INTO
        ArchiveOutput
    FROM
        Input TIMESTAMP BY Time
    
    SELECT
        Make,
        System.TimeStamp AS Time,
        COUNT(*) AS [Count]
    INTO
        AlertOutput
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
    HAVING
        [Count] >= 3

**說明**:
INTO 子句會告訴串流分析輸出，以便從這個陳述式將資料寫入其中。
第一個查詢將我們接收到的資料傳遞至我們命名為 ArchiveOutput 的輸出。
第二個查詢會執行一些簡單的彙總和篩選，並將結果傳送至下游的警示系統。
*注意*: 您也可以在多個輸出陳述式使用 Cte 的結果 (例如 WITH 陳述式)，因為這包含開啟較少到輸入來源的讀取器的好處。
例如:

    WITH AllRedCars AS (
        SELECT
            *
        FROM
            Input TIMESTAMP BY Time
        WHERE
            Color = 'red'
    )
    SELECT * INTO HondaOutput FROM AllRedCars WHERE Make = 'Honda'
    SELECT * INTO ToyotaOutput FROM AllRedCars WHERE Make = 'Toyota'

## 查詢範例：計算唯一值

**描述**: 計算會出現在資料流的時間間隔內的唯一值數目。
例如: 如何許多唯一的汽車通過收費亭 2 的第二個視窗中進行?

**輸入**：

| 請確定| 時間|
| --- | --- |
| Honda| 2015-01-01T00:00:01.0000000Z|
| Honda| 2015-01-01T00:00:02.0000000Z|
| Toyota| 2015-01-01T00:00:01.0000000Z|
| Toyota| 2015-01-01T00:00:02.0000000Z|
| Toyota| 2015-01-01T00:00:03.0000000Z|

**輸出：**

| 計數| 時間|
| --- | --- |
| 2| 2015-01-01T00:00:02.000Z|
| 1| 2015-01-01T00:00:04.000Z|

**解決方法：**

    WITH Makes AS (
        SELECT
            Make,
            COUNT(*) AS CountMake
        FROM
            Input TIMESTAMP BY Time
        GROUP BY
              Make,
              TumblingWindow(second, 2)
    )
    SELECT
        COUNT(*) AS Count,
        System.TimeStamp AS Time
    FROM
        Makes
    GROUP BY
        TumblingWindow(second, 1)

**說明:**
若要執行初始彙總在視窗上取得唯一的品牌，利用其計數。
然後我們會執行取得多少品牌的彙總 – 假設一個視窗中的所有唯一值都取得相同的時間戳記，則第二個彙總視窗必須是最小的，才不會從第一個步驟彙總 2 個視窗。

## 查詢範例：判斷值是否已變更

**描述**: 查看先前的值，以判斷它是否與目前的值不同
例如: 位於相同做為目前的汽車收費道路一輛車嗎?

**輸入**：

| 請確定| 時間|
| --- | --- |
| Honda| 2015-01-01T00:00:01.0000000Z|
| Toyota| 2015-01-01T00:00:02.0000000Z|

**輸出**：

| 請確定| 時間|
| --- | --- |
| Toyota| 2015-01-01T00:00:02.0000000Z|

**解決方案**：

    SELECT
        Make,
        Time
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make

**說明**:
使用 LAG 查看一個事件前的輸入資料流並取得品牌值。 然後將其和目前事件中的品牌比較，並且在兩者不同時輸出事件。

## 查詢範例：時間範圍內的第一個事件

**描述**：是否要每隔 10 分鐘尋找第一輛車？

**輸入**：

| LicensePlate| 請確定| 時間|
| --- | --- | --- |
| DXE 5291| Honda| 2015-07-27T00:00:05.0000000Z|
| YZK 5704| Ford| 2015-07-27T00:02:17.0000000Z|
| RMV 8282| Honda| 2015-07-27T00:05:01.0000000Z|
| YHN 6970| Toyota| 2015-07-27T00:06:00.0000000Z|
| VFE 1616| Toyota| 2015-07-27T00:09:31.0000000Z|
| QYF 9358| Honda| 2015-07-27T00:12:02.0000000Z|
| MDR 6128| BMW| 2015-07-27T00:13:45.0000000Z|

**輸出**：

| LicensePlate| 請確定| 時間|
| --- | --- | --- |
| DXE 5291| Honda| 2015-07-27T00:00:05.0000000Z|
| QYF 9358| Honda| 2015-07-27T00:12:02.0000000Z|

**解決方案**：

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) = 1

現在讓我們來變更問題，每隔 10 分鐘尋找特定品牌的第一輛車。

| LicensePlate| 請確定| 時間|
| --- | --- | --- |
| DXE 5291| Honda| 2015-07-27T00:00:05.0000000Z|
| YZK 5704| Ford| 2015-07-27T00:02:17.0000000Z|
| YHN 6970| Toyota| 2015-07-27T00:06:00.0000000Z|
| QYF 9358| Honda| 2015-07-27T00:12:02.0000000Z|
| MDR 6128| BMW| 2015-07-27T00:13:45.0000000Z|

**解決方案**：

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) OVER (PARTITION BY Make) = 1

## 查詢範例：時間範圍內的上一個事件

**描述**：每隔 10 分鐘尋找上一輛車。

**輸入**：

| LicensePlate| 請確定| 時間|
| --- | --- | --- |
| DXE 5291| Honda| 2015-07-27T00:00:05.0000000Z|
| YZK 5704| Ford| 2015-07-27T00:02:17.0000000Z|
| RMV 8282| Honda| 2015-07-27T00:05:01.0000000Z|
| YHN 6970| Toyota| 2015-07-27T00:06:00.0000000Z|
| VFE 1616| Toyota| 2015-07-27T00:09:31.0000000Z|
| QYF 9358| Honda| 2015-07-27T00:12:02.0000000Z|
| MDR 6128| BMW| 2015-07-27T00:13:45.0000000Z|

**輸出**：

| LicensePlate| 請確定| 時間|
| --- | --- | --- |
| VFE 1616| Toyota| 2015-07-27T00:09:31.0000000Z|
| MDR 6128| BMW| 2015-07-27T00:13:45.0000000Z|

**解決方案**：

    WITH LastInWindow AS
    (
        SELECT 
            MAX(Time) AS LastEventTime
        FROM 
            Input TIMESTAMP BY Time
        GROUP BY 
            TumblingWindow(minute, 10)
    )
    SELECT 
        Input.LicensePlate,
        Input.Make,
        Input.Time
    FROM
        Input TIMESTAMP BY Time 
        INNER JOIN LastInWindow
        ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
        AND Input.Time = LastInWindow.LastEventTime

**說明**:
在查詢中有兩個步驟 – 第一個 10 分鐘視窗中尋找最新時間戳記。 第二個步驟會聯結第一個查詢的結果與原始串流，以在每個視窗中尋找符合最後一個時間戳記的事件。

## 查詢範例：偵測到事件不存在

**描述**: 檢查資料流有沒有符合特定準則的值。
例如有 2 連續輛相同品牌的汽車進入收費道路 90 秒內?

**輸入**：

| 請確定| LicensePlate| 時間|
| --- | --- | --- |
| Honda| ABC-123| 2015-01-01T00:00:01.0000000Z|
| Honda| AAA-999| 2015-01-01T00:00:02.0000000Z|
| Toyota| DEF-987| 2015-01-01T00:00:03.0000000Z|
| Honda| GHI-345| 2015-01-01T00:00:04.0000000Z|

**輸出**：

| 請確定| 時間| CurrentCarLicensePlate| FirstCarLicensePlate| FirstCarTime|
| --- | --- | --- | --- | --- |
| Honda| 2015-01-01T00:00:02.0000000Z| AAA-999| ABC-123| 2015-01-01T00:00:01.0000000Z|

**解決方案**：

    SELECT
        Make,
        Time,
        LicensePlate AS CurrentCarLicensePlate,
        LAG(LicensePlate, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarLicensePlate,
        LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarTime
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make

**說明**:
使用 LAG 查看一個事件前的輸入資料流並取得品牌值。 然後將其和目前事件中的品牌比較，在兩者相同時輸出事件並使用 LAG 取得上一輛車的相關資料。

## 查詢範例：偵測某個情況的持續時間

**描述**: 了的解條件中發生的時間長度。
例如，假設一個錯誤，導致所有車輛的重量都不正確 (超過 20000 磅) – 我們想要計算錯誤的持續時間。

**輸入**：

| 請確定| 時間| 重量|
| --- | --- | --- |
| Honda| 2015-01-01T00:00:01.0000000Z| 2000|
| Toyota| 2015-01-01T00:00:02.0000000Z| 25000|
| Honda| 2015-01-01T00:00:03.0000000Z| 26000|
| Toyota| 2015-01-01T00:00:04.0000000Z| 25000|
| Honda| 2015-01-01T00:00:05.0000000Z| 26000|
| Toyota| 2015-01-01T00:00:06.0000000Z| 25000|
| Honda| 2015-01-01T00:00:07.0000000Z| 26000|
| Toyota| 2015-01-01T00:00:08.0000000Z| 2000|

**輸出**：

| StartFault| EndFault| FaultDurationSeconds|
| --- | --- | --- |
| 2015-01-01T00:00:01.0000000Z| 2015-01-01T00:00:08.0000000Z| 7|
| 2015-01-01T00:00:01.0000000Z| 2015-01-01T00:00:08.0000000Z| 7|
| 2015-01-01T00:00:01.0000000Z| 2015-01-01T00:00:08.0000000Z| 7|
| 2015-01-01T00:00:01.0000000Z| 2015-01-01T00:00:08.0000000Z| 7|
| 2015-01-01T00:00:01.0000000Z| 2015-01-01T00:00:08.0000000Z| 7|
| 2015-01-01T00:00:01.0000000Z| 2015-01-01T00:00:08.0000000Z| 7|

**解決方案**：

    SELECT
        PrevGood.Time AS StartFault,
        ThisGood.Time AS Endfault,
        DATEDIFF(second, PrevGood.Time, ThisGood.Time) AS FaultDuraitonSeconds
    FROM
        Input AS ThisGood TIMESTAMP BY Time
        INNER JOIN Input AS PrevGood TIMESTAMP BY Time
        ON DATEDIFF(second, PrevGood, ThisGood) BETWEEN 1 AND 3600
        AND PrevGood.Weight < 20000
        INNER JOIN Input AS Bad TIMESTAMP BY Time
        ON DATEDIFF(second, PrevGood, Bad) BETWEEN 1 AND 3600
        AND DATEDIFF(second, Bad, ThisGood) BETWEEN 1 AND 3600
        AND Bad.Weight >= 20000
        LEFT JOIN Input AS MidGood TIMESTAMP BY Time
        ON DATEDIFF(second, PrevGood, MidGood) BETWEEN 1 AND 3600
        AND DATEDIFF(second, MidGood, ThisGood) BETWEEN 1 AND 3600
        AND MidGood.Weight < 20000
    WHERE
        ThisGood.Weight < 20000
        AND MidGood.Weight IS NULL

**說明**:
我們要尋找的 2 個良好事件與錯誤事件之間並不是良好事件之間亦即 2 個事件是第一個事件之前和之後至少 1 個錯誤事件。 取得 2 個良好事件且兩者之間包含 1 個錯誤事件很容易，只要使用 2 個 JOIN，並藉由檢查重量和比較時間戳記來驗證我們已取得良好 -> 錯誤 -> 良好事件。

藉由使用我們在「左方外部聯結包含 NULL 或執行事件不存在」中了解的內容，我們知道要如何檢查我們挑選的 2 個良好事件之間沒有其他良好事件。

將這些內容撰寫在一起，我們就能取得良好 -> 錯誤 -> 良好事件，中間不會包含其他良好事件。 我們現在可以計算開頭和結尾良好事件之間的持續時間，進而取得錯誤的持續時間。

## 取得說明

如需進一步的協助，請嘗試我們 [Azure 串流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## 後續步驟

- [Azure 串流分析簡介](stream-analytics-introduction.md)
- [開始使用 Azure 串流分析](../stream.analytics.get.started.md)
- [調整 Azure 串流分析工作](stream-analytics-scale-jobs.md)
- [Azure 串流分析查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)






