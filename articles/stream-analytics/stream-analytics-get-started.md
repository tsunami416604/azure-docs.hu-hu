<properties
    pageTitle="開始使用串流分析：即時詐騙偵測 | Microsoft Azure"
    description="了解如何使用串流分析建立即時詐騙偵測解決方案。 使用事件中樞以即時處理事件。"
    keywords="anomaly detection, fraud detection, real time anomaly detection"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="paulettm"
    editor="cgronlun" />

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="12/04/2015"
    ms.author="jeffstok" />



# 開始使用 Azure 串流分析：即時詐騙偵測

了解如何利用 Azure 串流分析建立即時詐騙偵測的端對端解決方案。 將事件帶至 Azure 事件中樞、撰寫用於彙總或警示的串流分析查詢，以及將結果傳送到輸出接收器，以深入了解資料並即時處理。 已涵蓋電信的即時異常偵測，但範例技巧同樣適用於其他類型的詐騙偵測，例如信用卡或身分盜用案例。

串流分析是完全受管理的服務，可用來對雲端中的串流資料進行低延遲、高可用性、可調整的複雜事件處理。 如需詳細資訊，請參閱 [Azure 串流分析簡介](stream-analytics-introduction.md)。


## 案例：即時偵測電信與 SIM 詐騙

電信公司有大量的資料都是來電。 該公司需要下列資料:
* 削減管理數量向此資料，並取得一段時間和地理區域的客戶使用量的相關。
* 偵測 SIM 詐騙 (來自相同的識別大約在同一時間，但不同地理位置的多個呼叫) 中的即時，讓他們可以輕鬆地回應通知客戶或關閉服務。

物聯網 (IoT) 會產生大量遙測或感應器資料 – 而且客戶想要彙總這些資料或即時針對異常發出警示。

## 必要條件

這個案例利用 GitHub 上的事件產生器。 下載 [這裡](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TelcoGenerator) 並遵循此教學課程，設定您的方案中的步驟。

## 建立 Azure 事件中樞輸入與用戶群組

這個範例應用程式會產生事件並推送至事件中樞執行個體以供即時處理。 服務匯流排事件中心是慣用的資料流分析的事件擷取方法，而您可以進一步了解事件中樞在 [Azure 服務匯流排文件](/documentation/services/service-bus/)。

建立事件中樞：

1.  在 [Azure 入口網站](https://manage.windowsazure.com/) 按一下 **新增** > **應用程式服務** > **服務匯流排** > **事件中心** > **快速建立**。 提供名稱、區域及新的或現有命名空間，以建立新的事件中樞。  
2.  每個串流分析工作都應該從單一事件中樞取用者群組讀取資料，這才是最佳作法。 我們將逐步引導您建立下列的取用者群組的程序，而且可以 [深入了解取用者群組](https://msdn.microsoft.com/library/azure/dn836025.aspx)。 若要建立取用者群組，請瀏覽至剛剛建立的事件中樞，按一下 **取用者群組** 索引標籤，然後按一下 [ **建立** 在頁面底部，並提供取用者群組的名稱。
3.  為了授權存取事件中樞，我們需要建立一個共用存取原則。  按一下 [ **設定** 事件中樞] 索引標籤。
4.  在 **共用存取原則**, ，建立新的原則與 **管理** 權限。

    ![共用存取原則，您可以利用管理權限來建立原則。](./media/stream-analytics-get-started/stream-ananlytics-shared-access-policies.png)

5.  按一下 [ **儲存** 頁面的底部。
6.  瀏覽至 **儀表板** 按一下 **連接資訊** 底部的頁面，然後將複製並儲存連線資訊。

## 設定並啟動事件產生器應用程式

我們提供的用戶端應用程式將產生範例來電中繼資料，然後推播至事件中樞。 請依照下列步驟來設定此應用程式。  

1.  下載 TelcoGenerator 解決方案從 [https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TelcoGenerator](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TelcoGenerator)。
2.  將 App.Config 中的 Microsoft.ServiceBus.ConnectionString 與 EventHubName 值換成事件中樞連接字串與名稱。
3.  建立解決方案以觸發下載所需的 nuget 封裝。
4.  啟動應用程式。 使用方式如下：

        telcodatagen [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]

下列範例將在 2 小時的期間內產生 1000 個事件，當中的詐騙機率約為 20%：

    TelcoDataGen.exe 1000 .2 2

您將會看到傳到送事件中樞的記錄。 一些將在此即時詐騙偵測應用程式中使用的索引鍵欄位定義如下：

| Record | 定義 |
| ------------- | ------------- |
| CallrecTime | 電話開始時間的時間戳記。 |
| SwitchNum | 用來接通電話的電話交換機。 |
| CallingNum | 來電者的電話號碼。 |
| CallingIMSI | 國際行動用戶識別碼 (IMSI)。  來電者的唯一識別碼。 |
| CalledNum | 受話方的電話號碼。 |
| CalledIMSI | 國際行動用戶識別碼 (IMSI)。  受話方的唯一識別碼。 |


## 建立串流分析工作
現在，我們已經有了一連串的電信事件，我們可以設定串流分析工作來即時分析這些事件。

### 佈建資料流分析工作

1.  在 Azure 入口網站中，按一下 [ **新增 > Data Services > 資料流分析 > [快速建立**。
2.  指定下列值，然後再按一下 **建立串流分析工作**:

    * **工作名稱**: 輸入工作名稱。

    * **區域**: 選取您要執行工作的區域。 請考慮將工作和事件中樞放在相同的區域以確保更好的效能，以及在區域之間傳輸資料時無須付費。

    * **儲存體帳戶**: 選擇您想要使用的此區域內執行的所有串流分析工作儲存監視資料的 Azure 儲存體帳戶。 您可以選擇現有的儲存體帳戶，或建立新帳戶。

3.  按一下 [ **資料流分析** 以列出資料流分析工作的左窗格中。

    ![串流分析服務圖示](./media/stream-analytics-get-started/stream-analytics-service-icon.png)

4.  新工作將會顯示狀態為 **建立**。 請注意， **啟動** 頁面底部的按鈕會停用。 您必須先設定工作輸入、輸出、查詢等項目，才能啟動工作。

### 指定工作輸入
1.  在資料流分析工作按一下 **輸入** 上方的頁面上，然後按一下 [從 **加入輸入**。 開啟的對話方塊會逐步引導您完成設定輸入。
2.  選取 **資料流**, ，然後按一下向右按鈕。
3.  選取 **事件中心**, ，然後按一下向右按鈕。
4.  在第三頁上輸入或選取下列值：

    * **輸入別名**: 輸入此工作，例如輸入的易記名稱 *CallStream*。 請注意，此名稱將用於後續的查詢。
    * **事件中心**: 如果您建立的事件中樞與串流分析工作位於相同訂閱中，請選取事件中樞所在的命名空間。

    如果事件中樞位於不同的訂閱中，選取 **另一個訂用帳戶使用事件中心** 並手動輸入資訊 **服務匯流排命名空間**, ，**事件中樞名稱**, ，**事件中樞原則名稱**, ，**事件中樞原則金鑰**, ，和 **事件中心資料分割計數**。

    * **事件中樞名稱**: 選取事件中樞的名稱。

    * **事件中樞原則名稱**: 選取稍早在本教學課程中建立的事件中樞原則。

    * **事件中樞取用者群組**: 輸入稍早在本教學課程中所建立的取用者群組。
5.  按一下向右按鈕。
6.  指定下列值：

    * **事件序列化程式格式**: JSON
    * **編碼**: UTF8
7.  按一下核取按鈕以新增此來源，並確認 Stream Analytics 可成功連接到事件中樞。

### 指定工作查詢

資料流分析支援用來說明轉換的簡單、宣告式查詢模型，以供即時處理。 若要深入了解語言，請參閱 [Azure 資料流分析查詢語言參考](https://msdn.microsoft.com/library/dn834998.aspx)。 本教學課程將協助您撰寫和測試數個呼叫資料的即時串流查詢。

#### 選擇性：範例輸入資料
若要驗證對實際工作資料的查詢，您可以使用 **範例資料** 功能從您的資料流擷取事件，並建立。事件的 JSON 檔案進行測試。  下列步驟示範如何執行這項操作，而我們提供範例 [Telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json) 檔案以供測試用途。

1.  選取事件中樞輸入，然後按一下 [ **範例資料** 頁面的底部。
2.  在出現的對話方塊中，指定 **開始時間** 開始收集資料和 **持續時間** 要取用多少其他資料。
3.  按一下核取按鈕，以開始從輸入中的資料進行取樣。  產生資料檔案可能需要一或兩分鐘的時間。  處理程序完成後，按一下 **詳細資料** 然後下載並儲存。所產生的 JSON 檔案。

    ![下載並將處理的資料儲存在 JSON 檔案中](./media/stream-analytics-get-started/stream-analytics-download-save-json-file.png)

#### 傳遞查詢

如果您想要封存每一事件，您可以使用傳遞查詢，讀取事件或訊息承載中的所有欄位。 一開始會執行一項投射事件中所有欄位的簡單傳遞查詢。

1.  按一下 [ **查詢** 從資料流分析工作] 頁面的頂端。
2.  在程式碼編輯器中新增下列程式碼：

        SELECT * FROM CallStream

    > 請確定輸入來源的名稱符合您先前指定的輸入名稱。

3.  按一下 [ **測試** 查詢編輯器。
4.  提供測試檔案中，使用先前步驟所建立的其中一個，或是使用 [Telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json)。
5.  按一下核取按鈕，然後參閱查詢定義下方顯示的結果。

    ![查詢定義結果](./media/stream-analytics-get-started/stream-analytics-sim-fraud-output.png)


### 資料行投影

我們現在要削減傳回的欄位以成為更小的集合。

1.  在程式碼編輯器中，將查詢變更成：

        SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum
        FROM CallStream

2.  按一下 [ **重新執行** 查詢編輯器，即可看到查詢結果。

    ![查詢編輯器中的輸出。](./media/stream-analytics-get-started/stream-analytics-query-editor-output.png)

### 各區域的來電計數：含彙總的輪轉視窗

要比較量各區域的來電，我們會利用 [TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx) ，以取得依 SwitchNum 分組每隔 5 秒的來電計數。

1.  在程式碼編輯器中，將查詢變更成：

        SELECT System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount
        FROM CallStream TIMESTAMP BY CallRecTime
        GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum

    此查詢會使用 **Timestamp By** 關鍵字用於暫時運算時會在裝載中指定的時間戳記欄位。 如果未指定這個欄位，會根據事件到達事件中樞的時間，計算時間窗口。 請參閱 [< 到達時間與應用程式時間 」 中的資料流分析查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)。

    請注意，您可以存取的每個視窗結束時間戳記使用 **System.Timestamp** 屬性。

2.  按一下 [ **重新執行** 查詢編輯器，即可看到查詢結果。

    ![Timestand By 的查詢結果](./media/stream-analytics-get-started/stream-ananlytics-query-editor-rerun.png)

### 使用自我聯結執行 SIM 詐騙偵測

為了識別潛在的詐騙使用量，我們將尋找源自相同使用者卻在 5 秒內於不同位置撥出的電話。  我們 [聯結](https://msdn.microsoft.com/library/azure/dn835026.aspx) 本身，檢查是否在這些情況下呼叫事件資料流。

1.  在程式碼編輯器中，將查詢變更成：

        SELECT System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1,
        CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
        JOIN CallStream CS2 TIMESTAMP BY CallRecTime
        ON CS1.CallingIMSI = CS2.CallingIMSI
        AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
        WHERE CS1.SwitchNum != CS2.SwitchNum

2.  按一下 [ **重新執行** 查詢編輯器，即可看到查詢結果。

    ![聯結的查詢結果](./media/stream-analytics-get-started/stream-ananlytics-query-editor-join.png)

### 建立輸出接收

現在我們已經定義好一個事件資料流、一個負責擷取事件的事件中樞，以及一個進行資料流轉換處理的查詢，最後的步驟就是定義工作的輸出接收。  我們會將詐騙行為事件寫入  Blob 儲存體。

請按照下列步驟來建立 Blob 儲存體的容器 (如果沒有的話)。

1.  使用現有的儲存體帳戶，或按一下 [建立新的儲存體帳戶 **新增 > DATA SERVICES > 儲存體 > [快速建立** 並遵循指示。
2.  選取儲存體帳戶中，按一下 **容器** 在上方的頁面上，然後按一下 [ **新增**。
3.  指定 **名稱** ，容器並將其 **存取** 成公用 Blob。

## 指定工作輸出

1.  在資料流分析工作按一下 **輸出** 上方的頁面上，然後按一下 [從 **加入輸出**。 開啟的對話方塊會逐步引導您完成設定輸出。
2.  選取 **BLOB 儲存體**, ，然後按一下向右按鈕。
3.  在第三頁上輸入或選取下列值：

    * **輸出別名**: 輸入此工作輸出的易記名稱。
    * **訂閱**: 如果您建立的 Blob 儲存體中的資料流分析工作相同的訂閱，請選取 **目前訂用帳戶使用儲存體帳戶**。 如果您的儲存體屬於不同的訂閱，請選取 **從另一個訂用帳戶使用儲存體帳戶** 並手動輸入資訊 **儲存體帳戶**, ，**儲存體帳戶金鑰**, ，**容器**。
    * **儲存體帳戶**: 選取儲存體帳戶的名稱。
    * **容器**: 選取容器的名稱。
    * **檔案名稱前置詞**: 寫入 blob 輸出時要使用的檔案前置詞的型別。

4.  按一下向右按鈕。
5.  指定下列值：

    * **事件序列化程式格式**: JSON
    * **編碼**: UTF8

6.  按一下核取按鈕即可新增這個來源，並確認 Stream Analytics 可以成功連接到儲存體帳戶。

## 開始即時處理工作

既然工作輸入、查詢及輸出都已經指定好了，現在可以開始執行串流分析工作來進行即時詐騙偵測。

1.  從工作 **儀表板**, ，按一下 [ **啟動** 頁面的底部。
2.  在出現的對話方塊中，選取 **工作開始時間** ，然後按一下對話方塊底部的核取按鈕。 工作狀態會變更為 **起始** 且會很快就將 **執行**。

## 檢視詐騙偵測輸出

使用這類的工具 [Azure 儲存體總管](https://azurestorageexplorer.codeplex.com/) 或 [Explorer<](http://www.cerebrata.com/products/azure-explorer/introduction) 正在寫入至您輸出中即時檢視詐騙事件。  

![詐騙偵測：即時檢視的詐騙事件](./media/stream-analytics-get-started/stream-ananlytics-view-real-time-fraudent-events.png)

## 取得支援
如需進一步的協助，請嘗試我們 [Azure 串流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)。


## 後續步驟

- [Azure 資料流分析簡介](stream-analytics-introduction.md)
- [開始使用 Azure 資料流分析](stream-analytics-get-started.md)
- [調整 Azure 資料流分析工作](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

