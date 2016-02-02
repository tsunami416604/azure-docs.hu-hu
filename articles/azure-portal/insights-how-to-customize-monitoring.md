<properties 
    pageTitle="監視服務計量" 
    description="了解如何在 Azure 自訂監視圖表。" 
    authors="stepsic-microsoft-com" 
    manager="ronmart" 
    editor="" 
    services="azure-portal"
documentationCenter=""/>

<tags 
    ms.service="azure-portal" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2015" 
    ms.author="stepsic"/>


# 監視服務計量

所有的 Azure 服務都會追蹤重要計量，可讓您監視服務的健全狀況、效能、可用性和使用情況。

在某些服務中，您可能需要開啟診斷，才能查看任何計量。 在其他服務中 (例如虛擬機器)，您將會取得基本的計量集合，但需要啟用完整的高頻率計量集合。

## 使用監視圖表

您可以製作任何選擇時間期間上的計量圖表。

1. 

2. [**監視**] 區段中含有每個 Azure 資源最重要的計量。
    ![Monitoring lens](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)

3. 按一下任何一個圖表，即會顯示 [**計量**] 刀鋒視窗。 在此刀鋒視窗上，除了一個圖形之外，還會有一個顯示這些計量彙總 (例如選擇時間範圍上的平均值、最小值和最大值) 的資料表。
    ![Metric blade](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)

4. 若要自訂出現的資料行，請按一下圖表上的 [**編輯**] 按鈕，或按一下 [計量] 刀鋒視窗上的 [**編輯圖表**] 命令。

5. 在 [編輯查詢] 刀鋒視窗上，您可以執行三個動作：
    - 變更時間範圍
    - 在長條圖與折線圖之間切換外觀
    - 
    ![Edit Query](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)

6. 變更時間範圍十分容易，只要選取不同的範圍 (例如 [Past Hour]****)，再按一下刀鋒視窗底部的 [儲存]**** 即可。 您也可以選擇 [**自訂**]，這可讓您選擇過去 2 週的任何一段時間。 例如，您可以完整檢視這兩週，或僅檢視昨天的某 1 小時。 請在文字方塊中輸入不同的小時。
    ![Custom time range](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)

7. 在時間範圍下，您可以選擇任何要顯示在圖表上的度量數。

8. 當您按一下 [儲存] 時，系統便會儲存為該特定資源所做的變更。 例如，如果您有兩部虛擬機器，若變更其中一部虛擬機器的圖表，它將不會影響到另一部虛擬機器的圖表。

## 建立並排圖表

使用入口網站中的強大自訂功能，您可以新增任意數目的圖表。

1.   
    ![新增功能表](./media/insights-how-to-customize-monitoring/Insights_AddMenu.png)
2. 
    ![資源庫](./media/insights-how-to-customize-monitoring/Insights_Gallery.png)
3. 如果您沒有看到所需的計量，您可以隨時新增其中一個預設計量，並**編輯**圖表以顯示所需的計量。

## 監視使用量配額

大部分的計量會顯示某一段時間的趨勢，但是某些特定資料 (如使用量配額) 則會是包含臨界值的時間點資訊。

您也可以在刀鋒視窗上查看具有配額之資源的使用量配額：

![使用量](./media/insights-how-to-customize-monitoring/Insights_UsageChart.png)



## 後續步驟

* 
* 
* 
* 
* 
* 





