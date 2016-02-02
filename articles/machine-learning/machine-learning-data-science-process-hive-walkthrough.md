<properties
    pageTitle="Cortana 分析程序實務：使用 Hadoop 叢集 | Microsoft Azure"
    description="對採用 HDInsight Hadoop 叢集的端對端案例使用進階分析程序和技術 (ADAPT)，以使用公開可用的資料集來建置和部署模型。"
    services="machine-learning,hdinsight"
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
    ms.date="10/18/2015"
    ms.author="hangzh;bradsev" />



# Cortana 分析程序實務：使用 HDInsight Hadoop 叢集

在本逐步解說中，您可以使用 Cortana 分析程序，端對端案例使用 [Azure HDInsight Hadoop 叢集](http://azure.microsoft.com/services/hdinsight/) 來儲存、 探索和功能工程資料，可公開使用 [NYC 計程車車程](http://www.andresmh.com/nyctaxitrips/) 資料集，並縮減取樣資料。 資料的模型是使用 Azure Machine Learning 建置，以處理二元和多元分類和迴歸預測工作。

此逐步解說示範如何處理較大的 (1 tb) 資料集的類似案例中使用 HDInsight Hadoop 叢集的資料處理，請參閱 [Cortana 分析程序-1 TB 資料集上使用 Azure HDInsight Hadoop 叢集](machine-learning-data-science-process-hive-criteo-walkthrough.md)。

此外，也可以使用 1 TB 資料集，使用 IPython Notebook 來完成本逐步解說中說明的工作。 使用者想要嘗試這種方法，應該查閱 [使用 Hive ODBC 連線的 Criteo 逐步解說](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) 主題。


## <a name="dataset"></a>NYC 計程車車程資料集說明

「NYC 計程車車程」資料大約是 20GB 以逗號分隔值 (CSV) 的壓縮檔 (未壓縮時可達 48GB)，其中包含超過 1 億 7300 萬筆個別車程及針對每趟車程支付的費用。 每趟車程記錄包括上車和下車的位置與時間、匿名的計程車司機駕照號碼，以及圓形徽章 (計程車的唯一識別碼) 號碼。 資料涵蓋 2013 年的所有車程，並且每月會在下列兩個資料集中加以提供：

1. 'trip_data' CSV 檔案包含車程詳細資訊，例如乘客數、上車和下車地點、車程持續時間，以及車程長度。 以下是一些範例記錄：

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. 'trip_data' CSV 檔案包含針對每趟車程所支付的費用詳細資料，例如付款類型、費用金額、附加費和稅金、小費和通行費，以及支付的總金額。 以下是一些範例記錄：

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5


聯結 trip\_data and trip\_fare 的唯一索引鍵是由下列欄位所組成：medallion、hack\_licence 和 pickup\_datetime。

若要取得詳細資料的所有特定車程相關，就足以加入三個索引鍵:"medallion"、"hack\_license 」 和 「 pickup\_datetime 」。

稍後將這些資料儲存至 Hive 資料表時，我們將更詳細地加以說明。

## <a name="mltasks"></a>預測工作的範例

處理資料時，根據分析來決定您想要進行的預測種類，有助於釐清您必須在程序中包含的工作。
以下是三個預測問題，我們在構想根據此逐步解說的範例 *tip\_amount 來*:

1. **二元分類**: 預測是否已支付小費針對某趟車程，也就是 *tip\_amount 來* 大於 $0 高於為正面範例，而 *tip\_amount 來* $0 的為負面範例。

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0

2. **多元分類**：預測針對該趟車程支付的小費金額範圍。 我們將 *tip\_amount 來* 分成五個分類收納組或類別:

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20

3. **迴歸工作**：預測已針對某趟車程支付的小費金額。


## <a name="setup"></a>設定適用於進階分析 HDInsight Hadoop 叢集

>[AZURE.NOTE] 這通常是**管理**工作。

您可以採取三個步驟，為利用 HDInsight 叢集的進階分析設定 Azure 環境：

1. [建立儲存體帳戶](../storage-whatis-account.md): 此儲存體帳戶用於將資料儲存在 Azure Blob 儲存體。 HDInsight 叢集中使用的資料也位於此處。

2. [自訂 Azure HDInsight Hadoop 叢集的進階分析程序和技術](machine-learning-data-science-customize-hadoop-cluster.md)。 這個步驟會建立已在所有節點上安裝 64 位元 Anaconda Python 2.7 的 Azure HDInsight Hadoop 叢集。 自訂 HDInsight 叢集時應注意兩個重要的步驟。

    * 建立 HDInsight 叢集時，請務必將步驟 1 中建立的儲存體帳戶與您的 HDInsight 叢集連結。 這個儲存體帳戶是用來存取在叢集內處理的資料。

    * 建立叢集之後，請對叢集的前端節點啟用遠端存取。 巡覽至 [組態]**** 索引標籤，然後按一下 [啟用遠端]****。 這個步驟可指定使用於遠端登入的使用者認證。

3. [建立 Azure Machine Learning 工作區](machine-learning-create-workspace.md): 此 Azure Machine Learning 工作區用來建置機器學習模型。 使用 HDInsight 叢集完成初始資料探索和縮小取樣之後，會處理這項工作。

## <a name="getdata"></a>從公用來源取得資料

>[AZURE.NOTE] 這通常是**管理**工作。

若要取得 [NYC 計程車車程](http://www.andresmh.com/nyctaxitrips/) 的公用位置的資料集，您可以使用任何一種方法中所述 [移動資料的 Azure Blob 儲存體](machine-learning-data-science-move-azure-blob.md) 將資料複製到您的電腦。

我們在這裡說明如何使用 AzCopy 來傳輸含有資料的檔案。 若要下載並安裝 AzCopy，請遵循指示 [開始使用 AzCopy 命令列公用程式](../storage-use-azcopy.md)。

1. 從命令提示字元] 視窗中，發出下列 AzCopy 命令，將 *<path_to_data_folder>* 與所需的目的地:

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

2. 複製完成時，總共 24 個壓縮檔會位於選擇的資料夾中。將下載的檔案解壓縮到您本機電腦上的相同目錄。記下未壓縮檔案所在的資料夾。此資料夾將稱為 *<path\_to\_unzipped_data\_files\>* 下來。


## <a name="upload"></a>將資料上傳至 Azure HDInsight Hadoop 叢集預設容器

>[AZURE.NOTE] 這通常是**管理**工作。

在下列 AzCopy 命令中，以建立 Hadoop 叢集和解壓縮資料檔案時指定的實際值取代下列參數。

* *** & #60; path_to_data_folder > *** 包含未解壓縮的資料檔案之電腦上的目錄 (以及路徑)
* *** & #60，Hadoop 叢集的儲存體帳戶名稱 > *** 與 HDInsight 叢集相關聯的儲存體帳戶
* *** & #60，Hadoop 叢集預設容器 > *** 叢集所使用之預設容器。 請注意，預設容器的名稱通常與叢集本身的名稱相同。 例如，如果叢集稱為 "abc123.azurehdinsight.net"，預設容器即為 abc123。
* *** & #60; 儲存體帳戶金鑰 > *** 您叢集所使用的儲存體帳戶金鑰

從電腦的命令提示字元或 Windows PowerShell 視窗，執行下列兩個 AzCopy 命令。

此命令會上傳到的路線資料 *** nyctaxitripraw *** 目錄中的 Hadoop 叢集預設容器。

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv

此命令會上傳到將費用資料 *** nyctaxifareraw *** 目錄中的 Hadoop 叢集預設容器。

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

資料現在應該位於 Azure Blob 儲存體，而且準備好在 HDInsight 叢集內使用。

## <a name="#download-hql-files"></a>登入 Hadoop 叢集的前端節點，並準備將探索資料分析

>[AZURE.NOTE] 這通常是**管理**工作。

若要存取前端節點的叢集探索資料分析和縮小取樣的資料，請依照下列所述的程序 [存取 Hadoop 叢集的前端節點](machine-learning-data-science-customize-hadoop-cluster.md#headnode)。

在本逐步解說中，我們主要是使用中撰寫的查詢 [Hive](https://hive.apache.org/), ，類似 SQL 的查詢語言，來執行初步資料探索。 Hive 查詢會儲存在 .hql 檔案。 我們接著會縮小取樣這份資料，以便在 Azure Machine Learning 中用來建置模型。

若要準備叢集探索資料分析，我們可以下載包含從相關的 Hive 指令碼的.hql 檔案 [github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) 前端節點上的本機目錄 (C:\temp)。 若要這樣做，請從叢集的前端節點內開啟**命令提示字元**，並發出下列兩個命令：

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'
    
    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

這兩個命令會下載到本機目錄的這個逐步解說中所需的所有.hql 檔案 *** C:\temp & #92; *** 的前端節點中。

## <a name="#hive-db-tables"></a>建立 Hive 資料庫和資料表，根據月份分割

>[AZURE.NOTE] 這通常是**管理**工作。

我們現在已準備好為我們的 NYC 計程車資料集建立 Hive 資料表。
在 Hadoop 叢集前端節點，開啟 *** Hadoop 命令列 *** 桌面上的前端節點，並輸入以下命令來進入 Hive 目錄

    cd %hive_home%\bin

>[AZURE.NOTE] * * 所有 Hive 命令中都執行本逐步解說從上述的 Hive bin / 目錄提示字元。 如此可自動處理路徑相關問題。 我們使用詞彙 「 Hive 目錄提示 」、 「 Hive bin / 目錄提示 」，和 「 Hadoop 命令列 」 交換中此 walkthrough.* *

從 Hive 目錄提示字元，於前端節點的 Hadoop 命令列中輸入下列命令，以提交 Hive 查詢來建立 Hive 資料庫和資料表：

    hive -f "C:\temp\sample_hive_create_db_and_tables.hql"

以下是內容的 ***C:\temp\sample\_hive\_create\_db\_and\_tables.hql*** 檔案會建立 Hive 資料庫 *** nyctaxidb *** 和資料表 *** 路線 *** 和 *** 費用 ***。

    create database if not exists nyctaxidb;
    
    create external table if not exists nyctaxidb.trip
    (
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double)  
    PARTITIONED BY (month int)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');
    
    create external table if not exists nyctaxidb.fare
    (
        medallion string,
        hack_license string,
        vendor_id string,
        pickup_datetime string,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double)
    PARTITIONED BY (month int)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');

這個 Hive 指令碼會建立兩個資料表：

* "trip" 資料表包含每趟車程的詳細資料 (駕駛詳細資料、上車時間、車程距離和時間)
* "fare" 資料表包含費用詳細資料 (費用金額、小費金額、通行費及附加費)。

如果您需要使用這些程序的任何額外協助，或想要調查替代項目，請參閱節 [提交 Hive 查詢直接從 Hadoop 命令列 ](machine-learning-data-science-process-hive-tables.md#submit)。

## <a name="#load-data"></a>依資料分割資料載入 Hive 資料表

>[AZURE.NOTE] 這通常是**管理**工作。

NYC 計程車資料集會依月份自然分割資料，可讓我們更快速處理和查詢時間。 下列 PowerShell 命令 (使用 **Hadoop 命令列**從 Hive 目錄發出) 會將資料載入至依月份分割的 "trip" 和 "fare" Hive 資料表。

    for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

*Sample\_hive\_load\_data\_by\_partitions.hql* 檔案包含下列 **負載** 命令。

    LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
    LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

請注意，我們在這裡使用於探索程序中的許多 Hive 查詢，只會查看單一資料分割或幾個資料分割。 但這些查詢可跨整個資料執行。

### <a name="#show-db"></a>顯示 HDInsight Hadoop 叢集中的資料庫

若要在 Hadoop 命令列視窗內顯示在 HDInsight Hadoop 叢集中建立的資料庫，可在 Hadoop 命令列中執行下列命令：

    hive -e "show databases;"

### <a name="#show-tables"></a>顯示 nyctaxidb 資料庫中的 Hive 資料表

若要顯示 nyctaxidb 資料庫中的資料表，可在 Hadoop 命令列中執行下列命令：

    hive -e "show tables in nyctaxidb;"

我們可發出下列命令來確認資料表已分割：

    hive -e "show partitions nyctaxidb.trip;"

預期的輸出如下所示：

    month=1
    month=10
    month=11
    month=12
    month=2
    month=3
    month=4
    month=5
    month=6
    month=7
    month=8
    month=9
    Time taken: 2.075 seconds, Fetched: 12 row(s)

同樣地，我們可以發出下列命令來確保 fare 資料表已分割：

    hive -e "show partitions nyctaxidb.fare;"

預期的輸出如下所示：

    month=1
    month=10
    month=11
    month=12
    month=2
    month=3
    month=4
    month=5
    month=6
    month=7
    month=8
    month=9
    Time taken: 1.887 seconds, Fetched: 12 row(s)

## <a name="#explore-hive"></a>資料探索和 Hive 中的功能工程

>[AZURE.NOTE] 這通常是**資料科學家**工作。

適用於已載入 Hive 資料表之資料的資料探索和功能工程工作可以使用 Hive 查詢來完成。 以下是我們將在本節中逐步引導您執行的這類工作範例：

- 檢視這兩個資料表中的前 10 筆記錄。
- 在變動的時間範圍中探索數個欄位的資料分佈。
- 調查經度和緯度欄位的資料品質。
- 產生二進位和多類別分類標籤根據 **tip\_amount 來**。
- 藉由計算車程的直線距離來產生功能。

### 探索：檢視 trip 資料表中的前 10 筆記錄

>[AZURE.NOTE] 這通常是**資料科學家**工作。

為了查看資料的外觀，我們檢查每個資料表的 10 筆記錄。 從 Hadoop 命令列主控台的 Hive 目錄提示字元，個別執行下列兩個查詢來檢查記錄。

若要從 "trip" 資料表取得第一個月的前 10 筆記錄：

    hive -e "select * from nyctaxidb.trip where month=1 limit 10;"

若要從 "fare" 資料表取得第一個月的前 10 筆記錄：

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

通常是用於儲存記錄到檔案，以方便檢視。 對上述查詢進行微幅變更以完成這項作業：

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput

### 探索：檢視 12 個資料分割中每一個資料分割的記錄數目。

>[AZURE.NOTE] 這通常是**資料科學家**工作。

令人感興趣的是車程數目在日曆年度的變化。 依月份分組可讓我們查看這個車程的大約分佈情況。

    hive -e "select month, count(*) from nyctaxidb.trip group by month;"

這會提供輸出：

    1       14776615
    2       13990176
    3       15749228
    4       15100468
    5       15285049
    6       14385456
    7       13823840
    8       12597109
    9       14107693
    10      15004556
    11      14388451
    12      13971118
    Time taken: 283.406 seconds, Fetched: 12 row(s)

這裡的第一個資料行是月份，而第二個資料行是該月份的車程數目。

我們也可以在 Hive 目錄提示字元中發出下列命令，計算車程資料集的記錄總數。

    hive -e "select count(*) from nyctaxidb.trip;"

這會產生：

    173179759
    Time taken: 284.017 seconds, Fetched: 1 row(s)

我們可以使用針對車程資料集所顯示的類似命令，從 Hive 目錄提示字元發出費用資料集的 Hive 查詢，以驗證記錄數目。

    hive -e "select month, count(*) from nyctaxidb.fare group by month;"

這會提供輸出：

    1       14776615
    2       13990176
    3       15749228
    4       15100468
    5       15285049
    6       14385456
    7       13823840
    8       12597109
    9       14107693
    10      15004556
    11      14388451
    12      13971118
    Time taken: 253.955 seconds, Fetched: 12 row(s)

請注意，兩個資料集會傳回完全相同的每月車程數。 這會提供已正確載入資料的第一次驗證。

您可以從 Hive 目錄提示字元使用下列命令，來計算費用資料集中的記錄總數：

    hive -e "select count(*) from nyctaxidb.fare;"

這會產生：

    173179759
    Time taken: 186.683 seconds, Fetched: 1 row(s)

兩個資料表中的記錄總數也會相同。 這會提供資料已正確載入的第二次驗證。

### 探索：依據 medallion 的車程分佈

>[AZURE.NOTE] 這通常是**資料科學家**工作。

此範例會識別在特定期間內超過 100 趟車程的圓形徽章 (計程車數目)。 由於這個查詢受到資料分割變數 **month** 的條件約束，因此使用資料分割資料表會很有幫助。 查詢結果會寫入中的本機檔案 queryoutput.tsv `C:\temp` 前端節點上。

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

以下是內容的 *sample\_hive\_trip\_count\_by\_medallion.hql* 進行檢查的檔案。

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

NYC 計程車資料集中的 medallion 會識別唯一的計程車。 我們可以詢問哪些計程車在特定時段超過特定的車程數目，以識別哪些計程車「忙碌中」。 下列範例會識別在前三個月中超過幾百趟車程的計程車，並將查詢結果儲存至本機檔案 C:\temp\queryoutput.tsv。

以下是內容的 *sample\_hive\_trip\_count\_by\_medallion.hql* 進行檢查的檔案。

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

從 Hive 目錄提示字元發出下列命令：

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

### 探索：依據 medallion 和 hack_license 的車程分佈

>[AZURE.NOTE] 這通常是**資料科學家**工作。

當探索資料集時，我們通常會想要檢查共同出現的值群組數目。 本節提供如何為計程車和駕駛執行這項作業的範例。

*Sample\_hive\_trip\_count\_by\_medallion\_license.hql* 檔案群組"medallion"和"hack_license"費用資料集，並傳回每個組合的計數。 以下是其內容。

    SELECT medallion, hack_license, COUNT(*) as trip_count
    FROM nyctaxidb.fare
    WHERE month=1
    GROUP BY medallion, hack_license
    HAVING trip_count > 100
    ORDER BY trip_count desc;

這個查詢會依車程數目的遞減順序，傳回計程車和特定駕駛的組合。

從 Hive 目錄提示字元執行：

    hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv

查詢結果會寫入本機檔案 C:\temp\queryoutput.tsv。

### 探索：藉由檢查無效的經度/緯度記錄來評估資料品質

>[AZURE.NOTE] 這通常是**資料科學家**工作。

常見的探索資料分析目標是去掉無效或不正確的記錄。 本節的範例會判斷經度或緯度欄位是否包含遠離 NYC 區域的值。 由於這類記錄可能具有錯誤的經度-緯度值，因此我們想要從用來建立模型的任何資料中排除這些錯誤值。

以下是內容的 *sample\_hive\_quality\_assessment.hql* 進行檢查的檔案。

        SELECT COUNT(*) FROM nyctaxidb.trip
        WHERE month=1
        AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
        OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);

從 Hive 目錄提示字元執行：

    hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

這個命令中包含的 *-S* 引數會隱藏 Hive Map/Reduce 工作的狀態畫面顯示。 這非常有用，因為它讓 Hive 查詢輸出的螢幕顯示更容易閱讀。

### 探索：車程小費的二元類別分佈

**注意：**這通常是**資料科學家**工作。

二元分類問題中所述的 [預測工作的範例](machine-learning-data-science-process-hive-walkthrough.md#mltasks) ] 區段中，最好要知道是否已指定小費。 小費是二元分佈：

* 指定小費 (類別 1、 [tip\_amount > 美金 $0)
* 沒有小費 (類別 0、 [tip\_amount = 美金 $0)。

*Sample\_hive\_tipped\_frequencies.hql* 檔案如下所示的做法。

    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

從 Hive 目錄提示字元執行：

    hive -f "C:\temp\sample_hive_tipped_frequencies.hql"

### 探索：多元設定中的類別分佈

**注意：**這通常是**資料科學家**工作。

多類別分類問題中所述的 [預測工作的範例](machine-learning-data-science-process-hive-walkthrough.md#mltasks) 這個資料集也可用於自然分類，我們想要預測指定的小費金額的區段。 我們可以使用二元分類來定義查詢中的小費範圍。 若要取得各種小費範圍的類別分佈，我們使用 *sample\_hive\_tip\_range\_frequencies.hql* 檔案。 以下是其內容。

    SELECT tip_class, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount=0, 0,
            if(tip_amount>0 and tip_amount<=5, 1,
            if(tip_amount>5 and tip_amount<=10, 2,
            if(tip_amount>10 and tip_amount<=20, 3, 4)))) as tip_class, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tip_class;

從 Hadoop 命令列主控台執行下列命令：

    hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

### 探索：計算兩個經度-緯度位置之間的直線距離

**注意：**這通常是**資料科學家**工作。

直線距離的量值可協助我們了解與實際車程距離之間的差異。 這項特徵是用來指出當乘客認為駕駛刻意繞遠路時，他們可能比較不會給小費。

若要查看實際車程距離之間的比較和 [Haversine 距離](http://en.wikipedia.org/wiki/Haversine_formula) 之間兩個經度-緯度點 (「 大圓 」 距離)，我們使用可用三角函數內 Hive，因此:

    set R=3959;
    set pi=radians(180);
    
    insert overwrite directory 'wasb:///queryoutputdir'
    
    select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
    ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
     *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
     *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
     /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
     +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
     pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
    from nyctaxidb.trip
    where month=1
    and pickup_longitude between -90 and -30
    and pickup_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
    and dropoff_latitude between 30 and 90;

在上述查詢中，R 是地球的半徑 (英里)，而 pi 會轉換成弧度。 請注意，經度-緯度點會經過「篩選」，以移除遠離 NYC 區域的值。

在本例中，我們將結果寫入 "queryoutputdir" 目錄。 以下所示的一連串命令會先建立這個輸出目錄，然後再執行 Hive 命令。

從 Hive 目錄提示字元執行：

    hdfs dfs -mkdir wasb:///queryoutputdir
    
    hive -f "C:\temp\sample_hive_trip_direct_distance.hql"

查詢結果會寫入至 9 個 Azure blob *** queryoutputdir / 000000\_0 *** 至  *** queryoutputdir / 000008\_0 *** Hadoop 叢集預設容器底下。

為了查看個別 Blob 的大小，我們從 Hive 目錄提示字元執行下列命令：

    hdfs dfs -ls wasb:///queryoutputdir

若要查看指定檔案的內容，請說出 000000\_0，我們使用 Hadoop 的 `copyToLocal` 命令，因此。

    hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile

**警告:** `copyToLocal` 可能會非常慢的大型檔案，並不建議使用。

包含此資料位於 Azure blob 中的主要優點是我們可能使用 Azure Machine Learning 中的，資料瀏覽 [讀取器 ][reader] 模組。


## <a name="#downsample"></a>關閉 Azure Machine Learning 中的範例資料和組建模型

**注意：**這通常是**資料科學家**工作。

在探索資料分析階段之後，我們現在已準備好縮小取樣資料，以便在 Azure Machine Learning 中建置模型。 在本節中，我們示範如何使用 Hive 查詢來縮小取樣資料，然後透過 [讀取器 ][reader] Azure Machine Learning 中的模組。

### 縮小取樣資料

這個程序包含兩個步驟。 首先我們要加入 **nyctaxidb.trip** 和 **nyctaxidb.fare** 上的所有記錄中出現的三個索引鍵的資料表:"medallion"、"hack\_license 」 和 「 pickup\_datetime 」。 接著產生二進位分類標籤 **tipped** 和多類別分類標籤 **tip\_class**。

若要能夠使用縮小取樣資料直接從 [讀取器 ][reader] Azure Machine Learning 中的模組，就必須儲存到內部的 Hive 資料表將上述查詢的結果。 接下來我們將建立內部 Hive 資料表，並以加入和縮小取樣的資料來填入其內容。

查詢會套用標準 Hive 函數，來產生天、 週年，從 「 pickup\_datetime 」 欄位，以及上車與下車位置之間距離的工作日 (1 代表星期一和 7 代表星期日) 中的時間。 使用者可以參考 [LanguageManual UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) 的這類函式的完整清單。

查詢會接著縮小取樣資料，以將查詢結果填入 Azure Machine Learning Studio。 只有約 1% 的原始資料集會匯入 Studio。

以下是內容 *sample\_hive\_prepare\_for\_aml\_full.hql* 檔案，準備在 Azure Machine Learning 中建置模型的資料。

        set R = 3959;
        set pi=radians(180);
    
        create table if not exists nyctaxidb.nyctaxi_downsampled_dataset (
    
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        pickup_hour string,
        pickup_week string,
        weekday string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double,
        direct_distance double,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double,
        tipped string,
        tip_class string
        )
        row format delimited fields terminated by ','
        lines terminated by '\n'
        stored as textfile;
    
        --- now insert contents of the join into the above internal table
    
        insert overwrite table nyctaxidb.nyctaxi_downsampled_dataset
        select
        t.medallion,
        t.hack_license,
        t.vendor_id,
        t.rate_code,
        t.store_and_fwd_flag,
        t.pickup_datetime,
        t.dropoff_datetime,
        hour(t.pickup_datetime) as pickup_hour,
        weekofyear(t.pickup_datetime) as pickup_week,
        from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
        t.passenger_count,
        t.trip_time_in_secs,
        t.trip_distance,
        t.pickup_longitude,
        t.pickup_latitude,
        t.dropoff_longitude,
        t.dropoff_latitude,
        t.direct_distance,
        f.payment_type,
        f.fare_amount,
        f.surcharge,
        f.mta_tax,
        f.tip_amount,
        f.tolls_amount,
        f.total_amount,
        if(tip_amount>0,1,0) as tipped,
        if(tip_amount=0,0,
        if(tip_amount>0 and tip_amount<=5,1,
        if(tip_amount>5 and tip_amount<=10,2,
        if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class
    
        from
        (
        select
        medallion,
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        pickup_datetime,
        dropoff_datetime,
        passenger_count,
        trip_time_in_secs,
        trip_distance,
        pickup_longitude,
        pickup_latitude,
        dropoff_longitude,
        dropoff_latitude,
        ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
        *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
        *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
        +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance,
        rand() as sample_key
    
        from nyctaxidb.trip
        where pickup_latitude between 30 and 90
            and pickup_longitude between -90 and -30
            and dropoff_latitude between 30 and 90
            and dropoff_longitude between -90 and -30
        )t
        join
        (
        select
        medallion,
        hack_license,
        vendor_id,
        pickup_datetime,
        payment_type,
        fare_amount,
        surcharge,
        mta_tax,
        tip_amount,
        tolls_amount,
        total_amount
        from nyctaxidb.fare
        )f
        on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
        where t.sample_key<=0.01

若要從 Hive 目錄提示字元執行這個查詢：

    hive -f "C:\temp\sample_hive_prepare_for_aml_full.hql"

我們現在有內部資料表"nyctaxidb.nyctaxi_downsampled_dataset"，可以使用存取 [讀取器 ][reader] 從 Azure Machine Learning 模組。 此外，我們可能使用這個資料集來建置機器學習服務模型。

### 在 Azure Machine Learning 中，使用讀取器模組來存取縮小取樣的資料

發出 Hive 查詢中 [讀取器 ][reader] Azure Machine Learning 模組中的，我們需要存取 Azure Machine Learning 工作區以及叢集和與其相關聯的儲存體帳戶的認證來存取。

一些有關 [讀取器 ][reader] 模組和輸入參數:

**HCatalog 伺服器 URI**: 如果叢集名稱是 abc123，則這就是: https://abc123.azurehdinsight.net

**Hadoop 使用者帳戶名稱**：為叢集選擇的使用者名稱 (**非**遠端存取使用者名稱)

**Hadoop 使用者帳戶密碼**：為叢集選擇的密碼 (**非**遠端存取密碼)

**輸出資料的位置**：選擇為 Azure。

**Azure 儲存體帳戶名稱**：與叢集相關聯的預設儲存體帳戶名稱。

**Azure 容器名稱**：這是叢集的預設容器名稱，且通常與叢集名稱相同。 如果叢集為 "abc123"，即為 abc123。

**重要事項:** * * 我們想要使用查詢的任何資料表 [讀取器 ][reader] 判斷資料庫 D.db 中的資料表 T 是否為內部資料表，如下所示為 Azure Machine Learning 中的模組必須是在內部 table.* * 的提示。

從 Hive 目錄提示字元發出下列命令：

    hdfs dfs -ls wasb:///D.db/T

如果資料表是內部資料表並已填妥，其內容必須顯示在這裡。 判斷資料表是否為內部資料表的另一種方式是使用 Azure 儲存體總管。 您可以使用它巡覽至叢集的預設容器名稱，然後依資料表名稱篩選。 如果有顯示資料表及其內容，則可確認它是內部資料表。

以下是 Hive 查詢的快照集和 [讀取器 ][reader] 模組:

![](./media/machine-learning-data-science-process-hive-walkthrough/1eTYf52.png)

請注意，由於縮小取樣的資料位於預設容器中，從 Azure Machine Learning 的 Hive 查詢結果非常簡單而只是 「 選取 * 從 nyctaxidb.nyctaxi\_downsampled\_data 」。

資料集現在可做為建置機器學習服務模型的起點。

### <a name="mlmodel"></a>在 Azure Machine Learning 中建置模型

我們現在就能夠繼續進行模型建置和部署在 [Azure Machine Learning](https://studio.azureml.net)。 資料已就緒，可用來解決以上指出的預測問題：

**1. 二進位分類 * *: 預測是否已支付小費的路線。

**已使用學習者：**二元羅吉斯迴歸

a. 對於這個問題，我們的目標 (或類別) 標籤是 "tipped"。 縮小取樣的原始資料集有幾個資料行會顯示這個分類實驗目標。 特別是: tip\_class、 [tip\_amount] 和 [total\_amount 顯示測試時所沒有的目標標籤的相關資訊。 我們就會移除這些資料行考量使用 [專案資料行 ][project-columns] 模組。

下列快照集顯示我們的實驗，目的是預測是否會支付指定車程的小費。

![](./media/machine-learning-data-science-process-hive-walkthrough/QGxRz5A.png)

b. 對於這項實驗，我們的目標標籤分佈大約是 1:1。

下列快照集顯示二元分類問題之 tip 類別標籤的分佈。

![](./media/machine-learning-data-science-process-hive-walkthrough/9mM4jlD.png)

因此，我們將取得 0.987 的 AUC，如下圖所示。

![](./media/machine-learning-data-science-process-hive-walkthrough/8JDT0F8.png)

**2. 多元分類 * *: 預測針對該趟車程，使用先前定義的類別所支付的小費金額範圍。

**已使用學習者：**多元羅吉斯迴歸

a. 對於這個問題，我們的目標 (或類別) 標籤是 「 tip\_class 」，這個做法可能需要五個值 (0,1,2,3,4)。 如二元分類案例所示，我們有幾個資料行會顯示這個實驗的目標。 特別是: tipped、 [tip\_amount] total\_amount 顯示測試時所沒有的目標標籤的相關資訊。 我們會移除這些資料行使用 [專案資料行 ][project-columns] 模組。

下列快照集顯示我們的實驗來預測哪些 bin 秘訣是可能 (類別 0: 秘訣 = 美金 $0，類別 1: 秘訣 > 美金 $0 與提示 <= $5, Class 2 : tip > $5 與提示 <= $10, Class 3 : tip > $10 與提示 <= $20, Class 4 : tip > $20)

![](./media/machine-learning-data-science-process-hive-walkthrough/5ztv0n0.png)

現在會顯示實際的測試類別分佈。 我們看到類別 0 和類別 1 很普遍，其他類別則很罕見。

![](./media/machine-learning-data-science-process-hive-walkthrough/Vy1FUKa.png)

b. 對於這項實驗，我們使用混淆矩陣來查看預測精確度。 如下所示。

![](./media/machine-learning-data-science-process-hive-walkthrough/cxFmErM.png)

請注意，雖然常見類別上的類別精確度很高，但模型對較罕見類別沒有很好的「學習」效果。


**3. 迴歸工作 * *: 預測針對某趟車程支付的小費金額。

**已使用學習者：**推進式決策樹

a. 對於這個問題，我們的目標 (或類別) 標籤是 「 tip\_amount 來 」。 在此情況下是我們的目標流失: tipped、 tip\_class，total\_amount;這些變數會顯示測試時，通常無法使用之小費金額的相關資訊。 我們會移除這些資料行使用 [專案資料行 ][project-columns] 模組。

下列快照集顯示我們用來預測指定小費金額的實驗。

![](./media/machine-learning-data-science-process-hive-walkthrough/11TZWgV.png)

b. 對於迴歸問題，我們會藉由查看預測中的平方誤差、決定係數等，來測量預測的精確度。 以下將進行示範。

![](./media/machine-learning-data-science-process-hive-walkthrough/Jat9mrz.png)

我們看到決定係數是 0.709，其中隱含的變異大約有 71% 是由我們的模型係數所造成。

**重要事項:** 若要深入了解 Azure Machine Learning，以及如何存取並使用它，請參閱 [什麼是 Machine Learning?](machine-learning-what-is-machine-learning.md). 非常實用的資源進行眾多機器學習實驗，Azure Machine learning 是 [Cortana 分析圖庫](https://gallery.azureml.net/)。 該資源庫涵蓋了所有實驗，並且完整介紹了 Azure 機器學習的功能範圍。

## 授權資訊

此範例逐步解說及其隨附的指令碼是在 MIT 授權下由 Microsoft 所共用。 如需詳細資訊，請檢查 GitHub 上程式碼範例目錄中的 LICENSE.txt 檔案。

## 參考

•   [Andrés Monroy NYC 計程車車程下載頁面](http://www.andresmh.com/nyctaxitrips/)  
•   [FOILing NYC 的計程車車程資料 」 的 Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
•   [NYC 計程車和 Limousine 使用研究和統計資料](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)




[2]: ./media/machine-learning-data-science-process-hive-walkthrough/output-hive-results-3.png 
[11]: ./media/machine-learning-data-science-process-hive-walkthrough/hive-reader-properties.png 
[12]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-training.png 
[13]: ./media/machine-learning-data-science-process-hive-walkthrough/create-scoring-experiment.png 
[14]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-scoring.png 
[15]: ./media/machine-learning-data-science-process-hive-walkthrough/amlreader.png 
[project-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/ 
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/ 

