<properties
    pageTitle="開始使用 Azure Data Factory"
    description="本教學課程示範如何建立使用 Azure HDInsight 轉換資料的範例資料管線。"
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
    ms.topic="hero-article" 
    ms.date="11/02/2015"
    ms.author="spelluru"/>

# 開始使用 Azure Data Factory
> [AZURE.SELECTOR]
- [教學課程概觀](data-factory-build-your-first-pipeline.md)
- [使用 Data Factory 編輯器](data-factory-build-your-first-pipeline-using-editor.md)
- [使用 PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [使用 Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [使用資源管理員範本](data-factory-build-your-first-pipeline-using-arm.md)

本文可協助您開始建置您的第一個 Azure Data Factory。 

> [AZURE.NOTE] 本文不提供 Azure Data Factory 服務的概觀。 如需服務的詳細概觀，請參閱 [簡介 Azure Data Factory](data-factory-introduction.md) 文件。

## 教學課程概觀
本教學課程將帶您進行使用管線建置您的第一個 Data Factory 所需的步驟。 您將會建立管線，並從頭開始指定所有需要的資源。

如果您想快速地探索 Data Factory 的各種功能，但不想從頭開始建立資料處理站，您可以使用我們在 Azure 入口網站中提供的範例。 請參閱 [Azure Data Factory 更新 ︰ 簡化的範例部署](http://azure.microsoft.com/blog/2015/04/24/azure-data-factory-update-simplified-sample-deployment/) 如何部署使用 Azure 入口網站的使用案例根據的範例。

## 必要條件
開始進行本教學課程之前，您必須具備下列條件：

1.  **Azure 訂用帳戶** -如果您沒有 Azure 訂用帳戶，您可以建立免費試用帳戶只需要幾分鐘的時間。 請參閱 [免費試用版](http://azure.microsoft.com/pricing/free-trial/) 發行項上您要如何取得免費試用帳戶。

2.  **Azure 儲存體** – 您將使用 Azure 儲存體帳戶將資料儲存在本教學課程。 如果您沒有 Azure 儲存體帳戶，請參閱 [建立儲存體帳戶](../storage-create-storage-account/#create-a-storage-account) 文件。 建立儲存體帳戶之後，您必須取得用來存取儲存體的帳戶金鑰。 請參閱 [檢視、 複製和重新產生儲存體存取金鑰](../storage-create-storage-account/#view-copy-and-regenerate-storage-access-keys)。

## 本教學課程涵蓋哪些內容？ 
Azure Data Factory 可讓您以資料驅動型工作流程的方式，撰寫資料移動和資料處理工作。 您將了解如何建置第一個管線，每個月使用 HDInsight 來轉換及分析 Web 記錄檔。  

在本教學課程中，您將執行下列步驟：

1.  建立資料處理站。
2.  建立下列連結服務：
    1.  **Azure 儲存體帳戶** – Azure 儲存體帳戶將用來儲存隨選 HDInsight 叢集所使用的檔案。
    2.  **隨選 HDInsight 叢集** – HDInsight 叢集將會啟動指定轉換和分析資料。
3.  建立輸出資料集 
4.  建立執行 Hive 指令碼的管線，且會將結果儲存在輸出資料集。 Hive 指令碼會先建立外部資料表，參考儲存在 Azure blob 儲存體中未經處理的 Web 記錄資料。 Hive 指令碼中的下一個步驟會將未經處理資料依年份和月份分割處理。

第一個管線，稱為 **MyFirstPipeline**, ，使用轉換及分析 web 記錄檔會部署為 HDInsight 叢集的一部分，並儲存在 Hive 活動 **/hdisamples/mahoutmoviedata/out</b> /hdisamples/websitelogsampledata/samplelog</b>/底下找到/**。 

![圖表檢視](./media/data-factory-build-your-first-pipeline/diagram-view.png)

Hive 指令碼執行後，結果將會儲存在 Azure blob 儲存體容器 ︰ **資料/partitioneddata**。

定義上的可用性 **AzureBlobOutput** Hive 活動的執行頻率會決定資料集。 在本教學課程中，此項目設定為每個月一次。

## 準備用於教學課程的 Azure 儲存體
開始教學課程之前，您必須準備內含教學課程所需檔案的 Azure 儲存體。

1. 啟動 **記事本** 並貼上下列 HQL 指令碼。 此 Hive 指令碼會建立兩個外部資料表 ︰ **WebLogsRaw** 和 **WebLogsPartitioned**。 按一下 [ **檔案** 功能表，然後選取 **另存新檔**。 切換至 **C:\adfgettingstarted** 硬碟機上的資料夾。 選取 **所有檔案 (*。*)* * 的 **檔案類型** 欄位。 輸入 **partitionweblogs.hql** 的 **檔案名稱**。 確認 **編碼** 對話方塊底部的欄位會設為 **ANSI**。 如果沒有，請將它設定為 **ANSI**。  
    
        set hive.exec.dynamic.partition.mode=nonstrict;
        
        DROP TABLE IF EXISTS WebLogsRaw; 
        CREATE TABLE WebLogsRaw (
          date  date,
          time  string,
          ssitename string,
          csmethod  string,
          csuristem  string,
          csuriquery string,
          sport int,
          susername string,
          cipcsUserAgent string,
          csCookie string,
          csReferer string,
          cshost  string,
          scstatus  int,
          scsubstatus  int,
          scwin32status  int,
          scbytes int,
          csbytes int,
          timetaken int
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        LINES TERMINATED BY '\n' 
        tblproperties ("skip.header.line.count"="2");
        
        LOAD DATA INPATH '/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log' OVERWRITE INTO TABLE WebLogsRaw;
        
        DROP TABLE IF EXISTS WebLogsPartitioned ; 
        create external table WebLogsPartitioned (  
          date  date,
          time  string,
          ssitename string,
          csmethod  string,
          csuristem  string,
          csuriquery string,
          sport int,
          susername string,
          cipcsUserAgent string,
          csCookie string,
          csReferer string,
          cshost  string,
          scstatus  int,
          scsubstatus  int,
          scwin32status  int,
          scbytes int,
          csbytes int,
          timetaken int
        )
        partitioned by ( year int, month int)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
        STORED AS TEXTFILE 
        LOCATION '${hiveconf:partitionedtable}';
        
        INSERT INTO TABLE WebLogsPartitioned  PARTITION( year , month) 
        SELECT
          date,
          time,
          ssitename,
          csmethod,
          csuristem,
          csuriquery,
          sport,
          susername,
          cipcsUserAgent,
          csCookie,
          csReferer,
          cshost,
          scstatus,
          scsubstatus,
          scwin32status,
          scbytes,
          csbytes,
          timetaken,
          year(date),
          month(date)
        FROM WebLogsRaw
     
2. 為教學課程準備 Azure 儲存體：
    1. 下載 [最新版本的 **AzCopy**](http://aka.ms/downloadazcopy), ，或 [最新預覽版本](http://aka.ms/downloadazcopypr)。 請參閱 [如何使用 AzCopy](../storage/storage-use-azcopy.md) 文章說明如何使用此公用程式。
    2. AzCopy 安裝之後，您可以在命令提示字元中執行下列命令，將其新增到系統路徑。 
    
            set path=%path%;C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy           

    3. 瀏覽至 c:\adfgettingstarted 資料夾，然後執行下列命令來將 Hive .HQL 檔案上傳到儲存體帳戶。 取代 **StorageAccountName** 的儲存體帳戶名稱和 **儲存體金鑰** 的儲存體帳戶金鑰。

            AzCopy /Source:. /Dest:https://<StorageAccountName>.blob.core.windows.net/script /DestKey:<Storage Key>

        > [AZURE.NOTE] 上述命令會建立一個名為容器 **指令碼** 在您的 Azure Blob 儲存體和複製 **partitionweblogs.hql** 檔案從本機磁碟機的 blob 容器。 
    >
    5. 檔案成功上傳之後，您會看見下列來自 AzCopy 的輸出。
    
            Finished 1 of total 1 file(s).
            [2015/06/15 15:47:13] Transfer summary:
            -----------------
            Total files transferred: 1
            Transfer successfully:   1
            Transfer skipped:        0
            Transfer failed:         0
            Elapsed time:            00.00:00:01

執行下列動作：

- 按一下 [ [使用 Data Factory 編輯器](data-factory-build-your-first-pipeline-using-editor.md) 頂端使用 Data Factory 編輯器是 Azure 傳統入口網站的一部分來執行本教學課程的連結。
- 按一下 [ [使用 PowerShell](data-factory-build-your-first-pipeline-using-powershell.md) 頂端使用 Azure PowerShell 執行教學課程連結。
- 按一下 [ [使用 Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) 頂端使用 Visual Studio 執行教學課程連結。 



