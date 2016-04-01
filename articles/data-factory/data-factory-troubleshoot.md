<properties 
    pageTitle="Azure 資料處理站的疑難排解" 
    description="了解如何使用 Azure Data Factory 進行問題的疑難排解。" 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/12/2015" 
    ms.author="spelluru"/>

# 資料處理站的疑難排解
您可以使用 Azure 傳統入口網站 (或) Azure PowerShell Cmdlet 來對 Azure Data Factory 問題進行疑難排解。 本主題逐步解說如何使用 Azure 傳統入口網站來對您使用 Data Factory 時遇到的錯誤進行快速疑難排解。 

## 問題：無法執行 Data Factory Cmdlet
如果您使用的是版本 < 1.0 的 Azure PowerShell 版本：
 
若要解決此問題，Azure 模式切換至 **AzureResourceManager**: 

啟動 **PowerShell** 並執行下列命令以切換至 **AzureResourceManager** 模式。Azure Data Factory cmdlet 可用於 **AzureResourceManager** 模式。

         switch-azuremode AzureResourceManager

## 問題：執行 Data Factory Cmdlet 時發生未授權錯誤
您在 Azure PowerShell 中可能未使用正確的 Azure 帳戶或訂用帳戶。 請使用下列 Cmdlet 來選取要用於 Azure PowerShell 的正確 Azure 帳戶和訂用帳戶帳戶。 

1. Add-AzureAccount - 使用正確的使用者識別碼和密碼
2. Get-AzureSubscription - 檢視帳戶的所有訂用帳戶。 
3. Select-AzureSubscription <subscription name> -選取正確的訂閱。 請使用您在 Azure 入口網站上用來建立 Data Factory 的相同帳戶。

## 問題：無法從 Azure 傳統入口網站啟動資料閘道快速安裝
資料閘道的快速安裝需要有 Internet Explorer 或 Microsoft ClickOnce 相容的 Web 瀏覽器。 如果無法啟動快速安裝，您可以

1. 如果在其他瀏覽器中失敗，請切換至 Internet Explorer。 或
2. 使用入口網站中相同刀鋒視窗上顯示的 [手動安裝] 連結進行安裝，然後複製畫面上提供的金鑰，並在資料管理閘道組態就緒時貼上。 如果沒有啟動，請檢查開始功能表中的 [Microsoft 資料管理閘道]，並在啟動時貼上金鑰。 


## 問題：無法從 Azure 傳統入口網站啟動認證管理員
當透過 Azure 傳統入口網站來設定或更新 SQL Server 連結服務時，將會啟動認證管理員應用程式以確保安全性。 這需要 Internet Explorer 或 Microsoft ClickOnce 相容的 Web 瀏覽器。 如果在其他瀏覽器中失敗，您可以切換至 Internet Explorer。

## 問題：無法連線到內部部署 SQL Server 
確認已安裝閘道的機器可連接到 SQL Server。 在安裝閘道失敗的電腦上，您可以

1. Ping 已安裝 SQL Server 的電腦。 或
2. 使用您在 Azure 傳統入口網站以 SQL Server Management Studio (SSMS) 指定的認證，嘗試連接到 SQL Server 執行個體。


## 問題：輸入配量一直處於 PendingExecution 或 PendingValidation 狀態

配量處於 **PendingExecution** 或 **PendingValidation** 狀態的原因很多，其中一個常見的原因是， **外部** 屬性未設定為 **true**。 產生的 Azure Data Factory 範圍外的任何資料集應該用來標記 **外部** 屬性。 這表示資料在外部，且未由 Data Factory 內的任何管線所支持。 資料配量會標示為 **準備** 一旦資料可在個別的存放區中。 

請參閱下列範例使用 **外部** 屬性。 您可以選擇性地指定 **externalData*** 當您將設定為 true 外部... 

請參閱中的資料表主題 [JSON 指令碼參考][json-scripting-reference] 如需有關此屬性的詳細資訊。
    
    {
      "name": "CustomerTable",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "MyLinkedService",
        "typeProperties": {
          "folderPath": "MyContainer/MySubFolder/",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": ";"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "externalData": {
            "dataDelay": "00:10:00",
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
          }
        }
      }
    }

 若要解決這個錯誤，將加入 **外部** 屬性和選用 **externalData** 區段至輸入資料表的 JSON 定義，並重新建立資料表。 

## 問題：混合式複製作業失敗
若要了解更多詳細資料：

1. 在已安裝閘道的電腦上，啟動 [資料管理閘道組態管理員]。 確認 **閘道名稱** 上設為邏輯閘道名稱 **Azure 傳統入口網站**, ，**閘道器金鑰狀態** 是 **註冊** 和 **服務狀態** 是 **已啟動**。 
2. 啟動 **事件檢視器**。 展開 **應用程式及服務記錄檔** 按一下 **資料管理閘道器**。 查看是否有任何與資料管理閘道相關的錯誤。 

## 問題：隨選 HDInsight 佈建失敗，發生錯誤

使用 HDInsightOnDemandLinkedService 類型的連結的服務時，您應該指定指向 Azure Blob 儲存體的 linkedServiceName。 此儲存體帳戶將用來複製隨選 HDInsight 叢集的所有記錄檔和支援檔案。  在 HDInsight 上會執行隨選佈建的活動，有時可能失敗並出現下列錯誤：

        Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.

此錯誤通常表示 linkedServiceName 中指定的儲存體帳戶的位置，與進行 HDInsight 佈建的資料中心位置不同。 比方說，如果您的 Azure Data Factory 位置是美國西部，且隨選 HDInsight 佈建動作會在美國西部，但是 Azure blob 儲存體帳戶位置設定為美國東部、 指定佈建將會失敗。

此外，還有第二個的 JSON 屬性 additionalLinkedServiceNames，可供隨選 HDInsight 中指定其他儲存體帳戶。 這些其他的連結儲存體帳戶應該與 HDInsight 叢集位在相同位置，否則會失敗，並產生同樣的錯誤。



## 問題：自訂活動失敗
在 Azure Data Factory 中使用自訂活動時 (管線活動類型 CustomActivity)，自訂應用程式會在 HDInsight 的指定連結服務中，以僅限 Map 串流 MapReduce 工作的形式執行。 

自訂活動執行時，都能夠擷取該輸出從 HDInsight 叢集，並且儲存在 Azure Data Factory *adfjobs* Azure Blob 儲存體帳戶中的儲存體容器。 如果發生錯誤，您可以閱讀的文字 **stderr** 失敗發生後，輸出文字檔案。 您可以在 Web 瀏覽器中從 Azure 傳統入口網站本身存取和讀取這些檔案，或使用儲存體總管工具，直接存取保留在 Azure Blob 儲存體內儲存體容器中的檔案。 

若要列舉並讀取特定自訂活動的記錄，您可以依照本頁稍後說明的其中一個逐步解說。 簡言之：

1.  在 Azure 傳統入口網站 **瀏覽** 找出您的 Data Factory。
2.  使用 **圖表** 按鈕來檢視 data factory 圖表，然後按一下 [ **資料集** 特定表格 **管線** 含有自訂活動。 
3.  在 **資料表** 刀鋒視窗中，按一下感興趣的配量 **問題配量** 調查的時間範圍。
4.  詳細 **資料配量** ] 刀鋒視窗會出現，其中可能列出多個 **活動執行** 配量。 按一下 [ **活動** 從清單中。 
5.   **活動執行詳細資料** ] 刀鋒視窗會出現。 它會列出 **錯誤訊息** 中間刀鋒視窗中，數個 **記錄檔** 列出與該活動執行相關的分葉的底部。
    - Logs/system-0.log
    - 狀態
    - Status/exit
    - Status/stderr
    - Status/stdout

6. 按一下第一個 **記錄檔** 清單，以及記錄檔中的項目會在新的刀鋒視窗中開啟顯示供您閱讀完整文字。 按一下每個記錄檔來檢閱其中文字。 文字檢視器刀鋒視窗會開啟。 您可以按一下 **下載** ] 按鈕，下載文字檔供您隨意離線檢視。  

一個 **常見的錯誤** 從自訂活動 
        封裝執行失敗，結束代碼為 '1'。 請參閱 ' wasb://adfjobs@storageaccount.blob.core.windows.net/PackageJobs/<guid>/<jobid>/ Status/stderr ' 以取得詳細資料。

若要查看這種錯誤的其他詳細資料，請開啟 **stderr** 檔案。 一個常見錯誤是逾時狀況，例如 ︰
        資訊 mapreduce。工作 ︰ 工作 Id: attempt_1424212573646_0168_m_000000_0，狀態 ︰ 失敗 
        AttemptID:attempt_1424212573646_0168_m_000000_0 600 秒後逾時

如果工作重試 3 次 (例如超過 30 分鐘或更久)，此相同錯誤可能會出現多次。 

此逾時錯誤指出發生 600 秒 (10 分鐘) 逾時。 通常這表示自訂 .Net 應用程式已有 10 分鐘未發出任何狀態更新。 如果應用程式停滯或等待某事物太久，10 分鐘逾時是一項安全機制，可防止無止境等待與耽擱 Azure Data Factory 管線。 

此逾時源自於自訂活動中連結的 HDInsight 叢集組態。 此設定是 **mapred.task.timeout**, ，預設為 600000 毫秒，如 Apache 預設設定中所述 ︰ http://hadoop.apache.org/docs/r2.4.0/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml

您可以在佈建 HDInsight 佈建叢集時，變更預設值以覆寫這個預設值。 當使用 Azure Data Factory 和 **HDInsight 隨** 連結服務，可以新增此 JSON 屬性於您的 HDInsightOnDemandLinkedService JSON 屬性。 例如，您可以使用此 JSON 屬性將值延長到 20 分鐘。
        
        "mapReduceConfiguration" :
        {
            "mapreduce.task.timeout":"1200000"
        }
        

組態屬性的詳細資訊和 JSON 來編輯這些 map 的完整範例減少查看 MSDN 文件中的範例 #3 這裡 https://msdn.microsoft.com/library/azure/dn893526.aspx

## 問題：PowerShell 要求失敗，錯誤為 400 要求錯誤「找不到已註冊的資源提供者...」

自 2015 年 3 月 10 日起，已中止 Azure Data Factory PowerShell 早期私人預覽版本 2014-05-01-preview、2014-07-01-preview 和 2014-08-01-preview。 我們建議您使用最新版的 ADF cmdlet，現在都屬於 Azure PowerShell 下載，從這個 URL http://go.microsoft.com/?linkid=9811175&clcid=0x409 下載 ︰ 

如果您使用已中止的 Azure PowerShell SDK 版本，可能會收到下列錯誤：

        HTTP/1.1 400 Bad Request
        Cache-Control: no-cache
        Pragma: no-cache
        Content-Type: application/json; charset=utf-8
        Expires: -1
        x-ms-request-id: e07181e4-e421-46be-8a08-1f71d5e90494
        x-ms-correlation-request-id: e07181e4-e421-46be-8a08-1f71d5e90494
        x-ms-routing-request-id: WESTUS:20150306T234829Z:e07181e4-e421-46be-8a08-1f71d5e90494
        Strict-Transport-Security: max-age=31536000; includeSubDomains
        Date: Fri, 06 Mar 2015 23:48:29 GMT
        Content-Length: 157
        {"error":{"code":"NoRegisteredProviderFound","message":"No registered resource provider found for location 'west US' and API version '2014-05-01-preview'."}}


## <a name="copywalkthrough"></a> 逐步解說：對資料複製的錯誤進行疑難排解
在本逐步解說中，您將從＜開始使用資料處理站＞一文中的教學課程來引用一個錯誤，然後了解如何使用 Azure 傳統入口網站對錯誤進行疑難排解。

### 必要條件
1. 完成本教學課程中的 [開始使用 Azure Data Factory][adfgetstarted] 文件。
2. 確認 **ADFTutorialDataFactory** 中產生資料 **emp** Azure SQL Database 中的資料表。  
3. 現在，請刪除 **emp** 資料表 (**卸除 emp 資料表**) 從 Azure SQL Database。 這會引發錯誤。
4. 執行下列命令 **PowerShell** 以更新管線的作用期間，它會嘗試將資料寫入 **emp** 已不存在的資料表。

         
        Set-AzureRmDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 –EndDateTime 2014-09-30 –Name ADFTutorialPipeline
    
    Replace **StartDateTime** value with the current day and **EndDateTime** value with the next day. 


### 使用 Azure 入口網站對錯誤進行疑難排解

1.  登入 [Azure 入口網站][azure-portal]。 
2.  按一下 [ **ADFTutorialDataFactory** 從 **儀表板**。 如果您沒有看見 data factory 連結上 **儀表板**, ，按一下 [ **瀏覽** 中樞，然後按一下 **一切**。 按一下 [ **Data factory...** 在 **瀏覽** 刀鋒視窗中，然後按一下 [ **ADFTutorialDataFactory**。
3.  請注意，您會看到 **錯誤** 上 **資料集** 並排顯示。 按一下 [ **錯誤**。 您應該會看到 **發生錯誤的資料集** 刀鋒視窗。

    ![發生錯誤的 Data Factory 連結][image-data-factory-troubleshoot-with-error-link]

4. 在 **資料集** with errors] 分頁中，按一下 [ **EmpSQLTable** 查看 **資料表** 刀鋒視窗。 

    ![[發生錯誤的資料集] 刀鋒視窗][image-data-factory-troubleshoot-datasets-with-errors-blade]

5. 在 **資料表** 刀鋒視窗中，您應該會看到問題配量，亦即，配量中有錯誤 **問題配量** 清單底部。 您也可以查看任何最近出現錯誤 **最近的配量** 清單。 按一下中的配量 **問題配量** 清單。 

    ![含有問題配量的資料表刀鋒視窗][image-data-factory-troubleshoot-table-blade-with-problem-slices]

    如果您按一下 **問題配量** （不在特定問題上），您會看到 **資料配量** ] 刀鋒視窗，然後按一下 [ **特定問題配量** 查看 **資料配量** 投影片所選的資料配量。

6. 在 **資料配量** 分頁 **EmpSQLTable**, ，您會看到所有 **活動執行** 底部清單中的配量。 按一下 [ **活動執行** 從失敗的清單。

    ![含有作用中執行的資料配量刀鋒視窗][image-data-factory-troubleshoot-dataslice-blade-with-active-runs]


7. 在 **活動執行詳細資料** 刀鋒視窗中活動執行您已選取，您應該會看到錯誤的詳細資訊。 在此案例中，您會看到 ︰ **無效的物件名稱 'emp'**。

    ![發生錯誤的活動執行詳細資料][image-data-factory-troubleshoot-activity-run-with-error]

若要解決此問題，請建立 **emp** 資料表中使用 SQL 指令碼從 [開始使用 Data Factory][adfgetstarted] 文件。


### 使用 Azure PowerShell Cmdlet 對錯誤進行疑難排解
1.  啟動 **Azure PowerShell**。 
3. 執行 Get-AzureRmDataFactorySlice 命令來查看配量及其狀態。 您應該會看到有以下狀態的配量：Failed。    

         
        Get-AzureRmDataFactorySlice -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime 2014-10-15

    Replace **StartDateTime** with the StartDateTime value you specified for the **Set-AzureRmDataFactoryPipelineActivePeriod**. 

        ResourceGroupName       : ADFTutorialResourceGroup
        DataFactoryName         : ADFTutorialDataFactory
        TableName               : EmpSQLTable
        Start                   : 10/15/2014 4:00:00 PM
        End                     : 10/15/2014 5:00:00 PM
        RetryCount              : 0
        Status                  : Failed
        LatencyStatus           :
        LongRetryCount          : 0

    Note the **Start** time for the problem slice (the slice with **Status** set to **Failed**) in the output. 
4. 現在，執行 **Get AzureRmDataFactoryRun** cmdlet 來取得有關配量活動執行詳細資料。
         
        Get-AzureRmDataFactoryRun -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime "10/15/2014 4:00:00 PM"

    值 **StartDateTime** 是您在先前步驟中記下錯誤/問題配量的開始時間。 date-time 應該用雙引號括住。
5. 您應該會看到關於此錯誤的詳細資料 (如下所示) 輸出：

        Id                      : 2b19475a-c546-473f-8da1-95a9df2357bc
        ResourceGroupName       : ADFTutorialResourceGroup
        DataFactoryName         : ADFTutorialDataFactory
        TableName               : EmpSQLTable
        ResumptionToken         :
        ContinuationToken       :
        ProcessingStartTime     : 10/15/2014 11:13:39 PM
        ProcessingEndTime       : 10/15/2014 11:16:59 PM
        PercentComplete         : 0
        DataSliceStart          : 10/15/2014 4:00:00 PM
        DataSliceEnd            : 10/15/2014 5:00:00 PM
        Status                  : FailedExecution
        Timestamp               : 10/15/2014 11:13:39 PM
        RetryAttempt            : 0
        Properties              : {}
        ErrorMessage            : Unknown error in CopyActivity: System.Data.SqlClient.SqlException (0x80131904): **Invalid object name 'emp'.**
                         at System.Data.SqlClient.SqlConnection.OnError(SqlException exception, Boolean
                      breakConnection, Action`1 wrapCloseInAction)
                         at System.Data.SqlClient.TdsParser.ThrowExceptionAndWarning(TdsParserStateObject stateObj,

 

## <a name="pighivewalkthrough"></a> 逐步解說：疑難排解 Hive/Pig 處理錯誤
本逐步解說提供搭配 Azure 入口網站和 Azure PowerShell 使用 Hive/Pig 處理對錯誤進行疑難排解。 


### 逐步解說：使用 Azure 傳統入口網站對 Pig/Hive 處理的錯誤進行疑難排解
在此案例中，因為 HDInsight 叢集中 Hive 處理失敗，資料集處於錯誤狀態。

1. 按一下 [ **錯誤** 上 **資料集** 磚 **DATA FACTORY** 首頁。

    ![[資料集] 磚的 [發生錯誤] 連結][image-data-factory-troubleshoot-walkthrough2-with-errors-link]

2. 在 **發生錯誤的資料集** 刀鋒視窗中，按一下 [ **資料表** 您感興趣。

    ![[發生錯誤的資料集] 刀鋒視窗][image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]

3. 在 **資料表** 分頁上，按一下 **問題配量** 與 **狀態** 設 **失敗**。

    ![含有問題配量的資料表][image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]

4. 在 **資料配量** 刀鋒視窗中，按一下 [ **活動執行** 失敗。

    ![含有失敗執行的資料配量][image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]

5. 在 **活動執行詳細資料** 刀鋒視窗中，您可以下載與 HDInsight 處理相關聯的檔案。 按一下 [ **下載** 的 **Status/stderr** 以下載包含錯誤的詳細錯誤記錄檔。

    ![含有下載連結的活動執行詳細資料][image-data-factory-troubleshoot-activity-run-details]

    
### 逐步解說：使用 Azure PowerShell 對 Pig/Hive 處理的錯誤進行疑難排解
1.  啟動 **Azure PowerShell**。 
3. 執行 Get-AzureRmDataFactorySlice 命令來查看配量及其狀態。 您應該會看到有以下狀態的配量：Failed。    

         
        Get-AzureRmDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00

    Replace **StartDateTime** with the StartDateTime value you specified for the **Set-AzureRmDataFactoryPipelineActivePeriod**. 

        ResourceGroupName : ADF
        DataFactoryName   : LogProcessingFactory
        TableName         : EnrichedGameEventsTable
        Start             : 5/5/2014 12:00:00 AM
        End               : 5/6/2014 12:00:00 AM
        RetryCount        : 0
        Status            : Failed
        LatencyStatus     :
        LongRetryCount    : 0


    Note the **Start** time for the problem slice (the slice with **Status** set to **Failed**) in the output. 
4. 現在，執行 **Get AzureRmDataFactoryRun** cmdlet 來取得有關配量活動執行詳細資料。
         
        Get-AzureRmDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"

    值 **StartDateTime** 是您在先前步驟中記下錯誤/問題配量的開始時間。 date-time 應該用雙引號括住。
5. 您應該會看到關於此錯誤的詳細資料 (如下所示) 輸出：

        Id                  : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
        ResourceGroupName   : ADF
        DataFactoryName     : LogProcessingFactory3
        TableName           : EnrichedGameEventsTable
        ProcessingStartTime : 10/10/2014 3:04:52 AM
        ProcessingEndTime   : 10/10/2014 3:06:49 AM
        PercentComplete     : 0
        DataSliceStart      : 5/5/2014 12:00:00 AM
        DataSliceEnd        : 5/6/2014 12:00:00 AM
        Status              : FailedExecution
        Timestamp           : 10/10/2014 3:04:52 AM
        RetryAttempt        : 0
        Properties          : {}
        ErrorMessage        : Pig script failed with exit code '5'. See 'wasb://adfjobs@spestore.blob.core.windows.net/PigQuery
                                Jobs/841b77c9-d56c-48d1-99a3-8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for more details.
        ActivityName        : PigEnrichLogs
        PipelineName        : EnrichGameLogsPipeline
        Type                :

6. 您可以執行 **儲存 AzureRmDataFactoryLog** 指令程式具有識別碼值，上述輸出中看見的並下載記錄檔案所使用的 **-DownloadLogs** 指令程式的選項。



[adfgetstarted]: data-factory-get-started.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: http://go.microsoft.com/fwlink/?LinkId=516971

[azure-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png
 

