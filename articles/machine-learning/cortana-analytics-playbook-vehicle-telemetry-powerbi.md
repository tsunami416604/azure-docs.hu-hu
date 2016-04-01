<properties 
    pageTitle="車輛遙測分析方案範本 PowerBI 儀表板安裝指示 | Microsoft Azure" 
    description="使用 Cortana Analytics 具備的強大功能，取得關於車輛健全狀態與駕駛習慣的即時預測情資。" 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev" 
    manager="paulettm" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/02/2015" 
    ms.author="bradsev" />


# 車輛遙測分析方案範本 PowerBI 儀表板安裝指示

這 **功能表** 這個腳本章節的連結。 

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]


「車輛遙測分析」方案示範汽車經銷商、汽車製造商和保險公司如何運用 Cortana Analytics 功能，取得車輛健全狀態與駕駛習慣的即時預測情資，以改善客戶體驗、R&D 和行銷活動等方面。 
本文件包含在您的訂用帳戶中部署方案之後，如何設定 PowerBI 報告和儀表板的逐步指示。 


## 必要條件
1.  瀏覽至部署車輛遙測資料分析解決方案 [https://gallery.cortanaanalytics.com/SolutionTemplate/Vehicle-Telemetry-Analytics-3](https://gallery.cortanaanalytics.com/SolutionTemplate/Vehicle-Telemetry-Analytics-3)  
2.  [安裝 Microsoft Power BI Desktop](http://www.microsoft.com/download/details.aspx?id=45331)
3.   [Azure 訂用帳戶](https://azure.microsoft.com/pricing/free-trial/)。 如果您沒有 Azure 訂用帳戶，請立即取得免費的 Azure 訂用帳戶
4.  Microsoft PowerBI 帳戶
    

## Cortana Analytics Suite 元件
「車輛遙測分析」方案範本會在您的訂用帳戶中部署下列 Cortana Analytics 服務。

- **事件中樞** 數百萬車輛遙測事件擷取到 Azure。
- **串流分析**s 取得車輛健全狀況的即時深入資訊保存到長期的儲存體進行豐富的批次分析該資料。
- **機器學習** 以便進行異常偵測即時和批次處理，以深入了解預測。
- **HDInsight** 運用大規模的資料轉換
- **Data Factory** 處理協調流程排程、 資源管理和監視的批次處理管線。

**Power BI** 提供此解決方案即時資料和預測分析視覺效果豐富的儀表板。 

解決方案會使用兩個不同的資料來源 ︰ **模擬車輛訊號和診斷資料集** 和 **車輛目錄**。

此方案包含車輛遠程資訊服務模擬器。 它會在指定時間點發出對應於車輛狀態與駕駛模式的診斷資訊和訊號。 

「車輛類別目錄」是包含 VIN 至車型對應的參考資料集


## PowerBI 儀表板的準備工作

### 部署

完成部署後，您應該會看到下圖，其中以「綠色」標示所有這些元件。 

- 按一下綠色節點右上方的箭號，瀏覽至對應的服務，以驗證是否已成功部署所有這些元件。
- 按一下 [車輛遠程資訊服務模擬器] 節點右上方的箭號，下載資料模擬器封裝。 將檔案儲存在本機電腦上並解壓縮。 

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/1-deployed-components.png)

現在，您可以使用豐富的視覺效果來設定 PowerBI 儀表板，以取得車輛健全狀況和駕駛習慣的即時預測性情資。 建立所有報告和設定儀表板大約需要 45 分鐘至一小時。 


### 設定 Power BI 即時儀表板

**產生模擬資料**

1. 在本機電腦上，移至您解壓縮「車輛遠程資訊服務模擬器」封裝的資料夾![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/2-vehicle-telematics-simulator-folder.png)
2.  執行應用程式 ***CarEventGenerator.exe***。
3.  它會在指定時間點發出對應於車輛狀態與駕駛模式的診斷資訊和訊號。 這會發佈至您在部署時所設定的 Azure 事件中樞執行個體。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/3-vehicle-telematics-diagnostics.png)
     
**啟動即時儀表板應用程式**

解決方案包含一個可在 PowerBI 中產生即時儀表板的應用程式。 此應用程式會接聽由串流分析以連續模式發佈事件的事件中樞執行個體。 針對此應用程式收到的每一個事件，它會使用機器學習服務要求-回應評分端點來處理資料，而產生的資料集會發佈至 PowerBI 推送 API 來呈現視覺效果。 

若要下載應用程式：

1.  按一下 [圖表檢視上的 PowerBI 節點上，按一下 **下載即時儀表板應用程式**' [屬性] 窗格上的連結。![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard-new1.png)
2.  在本機擷取並儲存應用程式 ![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/4-real-time-dashboard-application.png)

3.  執行應用程式 **RealtimeDashboardApp.exe**
4.  提供有效的 Power BI 認證，登入，然後按一下 [ **接受**
    
    ![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
    
    ![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)


### 設定 PowerBI 報告
即時報告和儀表板需要大約 30-45 分鐘的時間才能完成。 
瀏覽至 [http://powerbi.com](http://powerbi.com) 與登入。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-1-powerbi-signin.png)

Power BI 中將會產生新的資料集。 按一下 [ **ConnectedCarsRealtime** 資料集。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

空白報表使用儲存 **Ctrl + s**。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

提供報表名稱 *車輛遙測分析即時的報告*。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)

## 即時報告
此解決方案有 3 份即時報告：

1.  行駛中的車輛
2.  需要維修的車輛
3.  車輛健全狀況統計資料

您可以選擇設定上述 3 份即時報告，或在任何階段後停止並繼續進行設定批次報告的下一節。 我們建議您建立上述 3 份報表，以將解決方案即時路徑的完整深入分析視覺化。  

### 1.行駛中的車輛
  
按兩下 **第 1 頁** 將它重新命名為"車輛作業中"  
    ![連接之車輛-作業中的車輛](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)  

選取 **vin** 欄位從 **欄位** ，然後選擇 [視覺效果類型 **"卡 」**。  

將建立卡片視覺效果，如圖所示。  
    ![Connected Cars - 選取 vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

按一下空白區域以加入新的視覺效果。  

選取 **城市** 和 **vin** 欄位。 變更視覺化 **「 對應 」**。 拖放到 **vin** 值] 區域中。 拖放到 **城市** 欄位從 **圖例** 區域。   
    ![連線的汽車-卡片視覺效果](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)
  
選取 **格式** > 一節 **視覺效果**, ，按一下 [ **標題** 和變更的文字 **"車輛運作的縣 （市） 」**。  
    ![連線的汽車-車輛運作的縣 （市）](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)   

最後一個視覺效果看起來如圖所示。    
    ![Connected Cars - 最終視覺效果](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

按一下空白區域以加入新的視覺效果。  

選取 **城市** 和 **vin**, ，視覺效果類型變更為 **群組直條圖**。 請確定 **縣 （市)** 欄位 **軸 」 區域** 和 **vin** 中 **值區域**  

排序圖表的 **「 計數的 vin 」**  
    ![連線的汽車-vin 的計數](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)  

變更圖表 **標題** 至 **"車輛運作的縣 （市） 」**  

按一下 [ **格式** 區段，然後選取 **資料色彩**,  ，按一下 [ **「 On 」** 至 **全部顯示**  
    ![連線的汽車-顯示所有資料的色彩](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)  

按一下色彩圖示變更個別的城市的色彩。  
    ![Connected Cars - 變更色彩](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)  

按一下空白區域以加入新的視覺效果。  

選取 **群組直條圖** 拖曳視覺效果的視覺效果，從 **城市** 欄位 **軸** 區域中， **模型** 中 **圖例** 區域和 **vin** 中 **值** 區域。  
    ![連線的汽車-群組的直條圖](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png)  
    ![連線的汽車-轉譯](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)
  
重新整理此頁面上的所有視覺效果，如圖所示。  
    ![Connected Cars -視覺效果](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

您已經成功設定「行駛中的車輛」即時報告。 您可以繼續建立下一份即時報告，或在此停止並設定儀表板。 

### 2.需要維修的車輛
  
按一下 [ ![新增](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) 若要加入新的報表，重新命名為 **「 車輛需要維護 」**

![Connected Cars - 需要維修的車輛](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)  

選取 **vin** 欄位和視覺效果類型變更為 **卡**。  
    ![連線的汽車-Vin 卡片視覺效果](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)  

我們在資料集中有一個名為 "MaintenanceLabel" 的欄位。 此欄位的值可以是 “0” 或 “1”。 它是由佈建為解決方案一部分並與即時路徑整合的 Azure Machine Learning 模型設定。 "1" 表示車輛需要維修。 

我們將 **頁面層級** 顯示需要維護的車輛資料篩選條件。  

1. 拖放到 **"MaintenanceLabel"** 欄位到 **頁面層級篩選**。  
![連線的汽車-頁面層級篩選](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)  

2. 按一下 [ **基本篩選** 功能表出現在底部 MaintenanceLabel 頁面層級篩選。  
![連線的汽車-基本篩選](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png)  

3.  若要設定其篩選值 **"1"**    
![連線的汽車-篩選值](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)  


按一下空白區域以加入新的視覺效果。  

選取 **群組直條圖** 從視覺效果  
![連線的汽車-Vind 卡片視覺效果](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png)  
![連線的汽車-群組的直條圖](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

拖曳欄位 **模型** 到 **軸** 區域中， **Vin** 至 **值** 區域。 將排序的視覺效果 **計數 vin**。  變更圖表 **標題** 至 **"車輛模型需要維護 」**  

拖放到 **vin** 欄位到 **色彩飽和度** 出現在 **欄位** ![欄位](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png) 區段 **視覺化** ] 索引標籤  
![連線的汽車-色彩飽和度](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)  

變更 **資料色彩** 從視覺效果中 **格式** 區段  
變更最小值色彩 ︰ **F2C812**  
變更最大值色彩 ︰ **FF6300**  
![連線的汽車-色彩變更](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png)  
![連線的汽車-新的視覺效果色彩](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)  

按一下空白區域以加入新的視覺效果。  

選取 **叢集直條圖** 視覺效果，從拖曳 **vin** 欄位到 **值** 區域中，拖曳 **城市** 欄位到 **軸** 區域。 排序圖表的 **「 計數的 vin 」**。 變更圖表 **標題** 至 **"車輛需要維護的縣 （市） 」**   
![連線的汽車-車輛需要維護的縣 （市）](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)  

按一下空白區域以加入新的視覺效果。  

選取 **多重資料列卡** 拖曳視覺效果的視覺效果，從 **模型** 和 **vin** 到 **欄位** 區域。  
![連線的汽車-多重資料列卡](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)    

重新整理所有視覺效果，最後的報表，如下所示的外觀  
![Connected Cars - 多列卡片](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)  

您已經成功設定「需要維修的車輛」即時報告。 您可以繼續建立下一份即時報告，或在此停止並設定儀表板。 

### 3.車輛健全狀況統計資料
  
按一下 [ ![新增](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) 若要加入新的報表，重新命名為 **"車輛健全狀況統計資料 」**  

選取 **量測計** 視覺效果，從視覺效果，然後拖曳 **速度** 到欄位
**值，最小值最大值** 區域。  
![連線的汽車-多重資料列卡](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)  

變更預設彙總的 **速度** 中 **值區域** 至 **平均** 

變更預設彙總的 **速度** 中 **最小區域** 至 **最小值**

變更預設彙總的 **速度** 中 **最大值區** 至 **上限**

![Connected Cars - 多列卡片](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)  

重新命名 **量測計標題** 至 **「 平均速度 」** 
 
![Connected Cars - 量測計](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)  

按一下空白區域以加入新的視覺效果。  

同樣地加入 **量測計** 的 **平均引擎石油**, ，**平均燃料**, ，和 **平均引擎 temperate**。  

變更預設彙總的每個量測計中依照上述步驟中的欄位 **「 平均速度 」** 量測計。

![Connected Cars - 量測計](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

按一下空白區域以加入新的視覺效果。

選取 **行與群組直條圖** 從視覺效果，然後拖曳 **縣 （市)** 欄位到 **共用軸**, ，拖曳 **速度**, ，**tirepressure 和 engineoil 欄位** 到 **資料行值** 區域中，變更其彙總類型 **平均**。 

拖放到 **engineTemperature** 欄位到 **列值** 區域中，將彙總類型變更為 **平均**。 

![Connected Cars -視覺效果欄位](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

變更圖表 **標題** 至 **「 平均速度、 輪胎壓力、 石油引擎和引擎溫度 」**  

![Connected Cars -視覺效果欄位](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

按一下空白區域以加入新的視覺效果。

選取 **Treemap** 拖曳視覺效果的視覺效果，從 **模型** 欄位到 **群組** 區域中，然後拖曳欄位 **MaintenanceProbability** 到 **值** 區域。

變更圖表 **標題** 至 **「 需要維護的車輛模型 」**

![Connected Cars - 變更圖表標題](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

按一下空白區域以加入新的視覺效果。

選取 **100%堆疊橫條圖** 從視覺效果的視覺效果，從拖曳 **城市** 欄位到 **軸** 區域中，然後拖曳 **MaintenanceProbability**, ，**RecallProbability** 欄位到 **值** 區域

![Connected Cars - 加入新的視覺效果](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

按一下 [ **格式**, ，請選取 **資料色彩**, ，變更 **MaintenanceProbability** 色彩值 **"F2C80F"**

變更 **標題** 圖表的 **"機率的車輛維護及回收的縣 （市) 」**  

![Connected Cars - 加入新的視覺效果](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

按一下空白區域以加入新的視覺效果。

選取 **區域圖** 從視覺效果的視覺效果，從拖曳 **模型** 欄位到 **軸** 區域中，然後拖曳 **engineOil，tirepressure，速度和 MaintenanceProbability** 欄位到 **值** 區域。 變更彙總類型，以 **「 平均數 」**。 

![Connected Cars - 變更彙總類型](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

變更圖表的標題 **「 平均引擎石油，對模型的壓力、 速度和維護的機率 」**

![Connected Cars - 變更圖表標題](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

按一下空白區域以加入新的視覺效果。

1. 選取 **散佈圖** 從視覺效果的視覺效果
2. 拖放到 **模型** 欄位到 **詳細資料** 和 **圖例** 區域 
3. 拖放到 **燃料** 欄位到 **X 軸** 區域中，變更為 [平均彙總
4. 拖放到 **engineTemparature** 到 **Y 軸 」 區域**, ，變更的彙總 **平均**
5. 拖曳 **vin** 欄位到 **大小** 區域

![Connected Cars - 加入新的視覺效果](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

變更圖表 **標題** 至 **「 燃料的平均值，引擎溫度模型 」**

![Connected Cars - 變更圖表標題](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

最終的報告顯示如下。

![Connected Cars - 最終報告](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### 將報告中的視覺效果釘選到即時儀表板
  
按一下 [儀表板] 旁邊的加號圖示，以建立空白儀表板。 您可以將它命名為「車輛遙測分析儀表板」

![Connected Cars - 儀表板](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.5.png)

將上述報告中的視覺效果釘選到儀表板。 
 
![Connected Cars - 儀表板](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.6.png)

已建立上述 3 份報告並將對應的視覺效果釘選到儀表板時，儀表板應如下所示。 如果您尚未建立所有報告，儀表板看起來可能有所不同。 

![Connected Cars - 儀表板](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)

恭喜！ 您已成功建立即時儀表板。 隨著您繼續執行 CarEventGenerator.exe 和 RealtimeDashboardApp.exe，您應該會在儀表板上看見即時更新。 
應該需要約 10 至 15 分鐘的時間才能完成下列步驟。
 
##  設定 Power BI 批次處理儀表板

注意 ︰ 花大約 2 小時 （從部署成功完成） 的端對端批次處理完成執行，然後處理值得一年，產生之資料的管線。 請稍候再進行後續步驟。 

**下載 PowerBI 設計工具檔案**
• 在預先設定的 PowerBI 設計工具的檔案已部署的一部分
• 按一下 [圖表] 檢視中的 [PowerBI] 節點，然後按一下 '下載 PowerBI 設計工具' 檔案連結屬性] 窗格 ![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9.5-download-powerbi-designer.png)

• 將儲存在本機

**設定 PowerBI 報告**
• 開啟設計工具的檔案 'VehicleTelemetryAnalytics-桌面 Report.pbix' 使用 PowerBI 桌面。 如果您還沒有，安裝 PowerBI 桌面從 [PowerBI 桌面安裝](http://www.microsoft.com/download/details.aspx?id=45331)。 

• 按一下 **編輯查詢**。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

- 連按兩下 [ **來源**。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

- 以佈建為部署一部分的 Azure SQL Server 更新伺服器連接字串。 按一下圖表上的 Azure SQL 節點，然後檢視 [屬性] 窗格的伺服器名稱。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11.5-view-server-name.png)

- 保留 **資料庫** 為 *connectedcar*。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

- 按一下 [ **確定**。
- 您會看到 **Windows 認證** 選取預設值] 索引標籤中，將它變更為 **資料庫認證** 上的 [ **資料庫** 右邊的索引標籤。
- 提供 **Username** 和 **密碼** Azure SQL 資料庫，其部署在安裝期間指定。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

- 按一下 [ **連接**
- 針對右窗格中出現的其餘 3 個查詢，重複上述步驟，並更新資料來源連接詳細資料。
- 按一下 [ **關閉並載入**。 Power BI Desktop 檔案資料集會連接到 SQL Azure 資料庫資料表。
- **關閉** Power BI Desktop 檔案。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

- 按一下 [ **儲存** ] 按鈕以儲存所做的變更。 
 
您現在已設定對應至解決方案中的批次處理路徑的所有報告。 


## 上傳至 *powerbi.com*
 
1.  瀏覽至 PowerBI web 入口網站，在 http://powerbi.com 和登入。
2.  按一下 [取得資料]  
3.  上傳 Power BI Desktop 檔案。  
4.  若要上傳，請按一下 **取得資料-> [檔案]-> [本機檔案**  
5.  瀏覽至 **"VehicleTelemetryAnalytics – 桌面 Report.pbix 」**  
6.  一旦上傳檔案，將會瀏覽回到您的 Power BI 工作區。  

將會為您建立資料集、報告和空白儀表板。  
 

圖表釘選到現有儀表板 **車輛遙測資料分析儀表板** 中 **Power BI**。 按一下上面建立的空白儀表板，然後瀏覽至 **報表** 區段按一下 [新上傳報表。  

![車輛遙測 PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png) 


**請注意報表有六個頁面 ︰**  
第 1 頁 ︰ 車輛密度  
第 2 頁 ︰ 即時車輛健全狀況  
第 3 頁 ︰ 積極驅動車輛   
第 4 頁 ︰ 回收車輛  
第 5 頁 ︰ 燃料有效率地驅動車輛  
第 6 頁 ︰ Contoso 標誌  

![Connected Cars PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)
 

**從第 3 頁**, ，釘選下列  
1.  VIN 的計數  
    ![Connected Cars PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png) 

2.  積極地驅動車輛模型 – 瀑布圖  
    ![車輛遙測 - 釘選圖表 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

**從第 5 頁**, ，釘選下列  
1.  Vin 的計數    
    ![車輛遙測 - 釘選圖表 5](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png)  
2.  油料有效率的車輛模型 ︰ 群組的直條圖  
    ![車輛遙測 - 釘選圖表 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

**從第 4 頁**, ，釘選下列  

1.  Vin 的計數  
    ![車輛遙測 - 釘選圖表 7](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 

2.  依城市來回收的車輛模型 ︰ Treemap  
    ![車輛遙測 - 釘選圖表 8](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)  

**從第 6 頁**, ，釘選下列  

1.  Contoso 馬達標誌  
    ![車輛遙測 - 釘選圖表 9](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

**組織儀表板**  

1.  瀏覽至儀表板
2.  將滑鼠暫留在每個圖表，根據下列完整儀表板影像中提供的名稱來重新命名。 也移動圖表看起來像下面的儀表板。  
    ![車輛遙測 - 組織儀表板 2](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png)  
    ![車輛遙測 PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)
3. 如果您已建立本文件中提到的所有報報，則最終完成的儀表板應該看起來如下。 

![車輛遙測 - 組織儀表板 2](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard3.png)

恭喜！ 您已成功建立報報和儀表板，可取得車輛健全狀況和駕駛習慣的即時、預測和批次深入分析。  


