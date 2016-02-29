<properties
   pageTitle="使用 Ambari 檢視與 HDInsight (Hadoop) 上的 Hive 搭配作業 |Microsoft Azure"
   description="了解如何從網頁瀏覽器使用 Hive 檢視來提交 Hive 查詢。 Hive 檢視是以 Linux 為基礎的 HDInsight 叢集隨附的 Ambari 檢視的一部分。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="12/03/2015"
   ms.author="larryfr"/>

#在 HDInsight 中搭配 Hadoop 使用 Hive 檢視

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Ambari 是隨著以 Linux 為基礎的 HDInsight 叢集提供的管理和監視公用程式。 透過 Ambari 提供的功能之一是可以用來執行 Hive 查詢的 Web UI。 這是 __Hive 檢視__, 、 Ambari 檢視隨附於 HDInsight 叢集的一部分。

> [AZURE.NOTE] Ambari 有很多不會在本文中討論的功能。 如需詳細資訊，請參閱 [管理 HDInsight 叢集使用 Ambari Web UI](hdinsight-hadoop-manage-ambari.md)。

##先決條件

- 以 Linux 為基礎的 HDInsight 叢集。 如需建立新的叢集資訊，請參閱 [開始使用以 Linux 為基礎的 HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)。

##開啟 Hive 檢視

您可以從 Azure 入口網站中; Ambari 檢視選取您的 HDInsight 叢集，然後選取 __Ambari 檢視__ 從 __快速連結__ 一節。

![快速連結區段](./media/hdinsight-hadoop-use-hive-ambari-view/quicklinks.png)

您也可以移至 https://CLUSTERNAME.azurehdinsight.net 網頁瀏覽器中瀏覽至 Ambari 直接 (其中 __CLUSTERNAME__ 是您的 HDInsight 叢集的名稱)，然後從 [頁面] 功能表中選取 [方形組 (旁邊 __管理__ 連結和頁面左邊的按鈕) 清單可用的檢視。 選取 __Hive 檢視__。

![選取 ambari 檢視](./media/hdinsight-hadoop-use-hive-ambari-view/selecthiveview.png).

> [AZURE.NOTE] 當存取 Ambari，系統會提示您驗證至站台。 輸入您在建立叢集時所使用的 admin (預設為 `admin`)、帳戶名稱和密碼。

您應該會看到如下所示的頁面：

![Hive 檢視頁面的影像，包含查詢編輯器區段](./media/hdinsight-hadoop-use-hive-ambari-view/hiveview.png)

##檢視資料表

在 __資料庫總管__ 部分頁面上，選取 __預設__ 上的項目 __資料庫__ ] 索引標籤。 這會顯示預設資料庫中的資料表清單。 針對新的 HDInsight 叢集，只能有一個資料表應該存在; __hivesampletable__。

![已展開預設資料庫的資料庫總管](./media/hdinsight-hadoop-use-hive-ambari-view/databaseexplorer.png)

透過這份文件中的步驟新增新的資料表時，您可以使用 [資料庫總管] 右上角的重新整理圖示，重新整理可用的資料表清單。

##<a name="hivequery"></a>查詢編輯器

從 Hive 檢視使用下列步驟，對叢集內含的資料執行 Hive 查詢。

1. 在 __查詢編輯器__ 區段的頁面上，將下列 HiveQL 陳述式貼到工作表:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

    這些陳述式會執行下列動作：

    - **DROP TABLE** -刪除資料表和資料檔中，如果資料表已存在。
    - **建立外部資料表** -在 Hive 中建立新的 「 外部 」 資料表。 外部資料表只會在 Hive 中儲存資料表定義；資料會保留在原始位置。
    - **ROW FORMAT** -告訴 Hive 如何格式化資料。 在此情況下，每個記錄中的欄位會以空格隔開。
    - **STORED AS TEXTFILE LOCATION** -告訴 Hive 資料的儲存 (example/data 目錄)，且儲存為文字。
    - **選取** -選擇其資料欄 t4 包含值 [ERROR] 的其中的所有資料列的計數。

    >[AZURE.NOTE] 當您預期以外部來源，例如自動化的資料上傳程序，或以其他 MapReduce 作業，更新基礎資料時，應該使用外部資料表，但您希望 Hive 查詢一律使用最新的資料。 捨棄外部資料表並 *不* 刪除資料，資料表定義。

2. 使用 __Execute__ 底部的 [查詢編輯器啟動查詢] 按鈕。 它應該會變成橘色，文字會變成 __停止執行__。 A __查詢程序結果__ 區段應該會出現在 [查詢編輯器] 下方，並顯示工作的相關資訊。

    > [AZURE.IMPORTANT] 某些瀏覽器可能無法正確重新整理記錄或結果的資訊。 如果您執行一項作業，而該作業似乎會一直執行，但未更新記錄檔或傳回結果，請嘗試改用 Mozilla FireFox 或 Google Chrome。
    
3. 當查詢完成之後時， __查詢程序結果__ 區段會顯示作業的結果。  __停止執行__ 按鈕也會變更回綠色 __Execute__ ] 按鈕。  __結果__ ] 索引標籤應該包含下列資訊:

        sev       cnt
        [ERROR]   3

     __記錄__ ] 索引標籤可以用來檢視工作所建立的記錄資訊。 您可以將此資訊用來排解查詢問題。
    
    > [AZURE.TIP] 請注意 __將結果儲存__ 下拉式清單對話方塊右上角的 __查詢程序結果__ 區段，您可用來下載結果，或將它們儲存到 HDInsight 儲存體為 CSV 檔案。
    
3. 選取此查詢中，前四行，然後選取 __Execute__。 請注意，作業完成時沒有任何結果。 這是因為使用 __Execute__ 按鈕選取查詢的一部分時，才會執行所選的陳述式。 在此情況下，選取項目不包含從資料表擷取資料列的最後一個陳述式。 如果您選取只那一行，並使用 __Execute__, ，您應該會看到預期的結果。

3. 使用 __新工作表__ 底部的按鈕 __查詢編輯器__ 來建立新的工作表。 在新的工作表中，輸入下列 HiveQL 陳述式：

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

    這些陳述式會執行下列動作：

    - **CREATE TABLE IF NOT EXISTS** -建立資料表，如果不存在。 由於 **外部** 未使用關鍵字，這是內部資料表，會儲存在 Hive 資料倉儲並完全透過 Hive 所管理。 與外部資料表不同，捨棄內部資料表也會同時刪除基礎資料。
    - **STORED AS ORC** -以最佳化資料列單欄式 (ORC) 格式儲存資料。 這是高度最佳化且有效率的 Hive 資料儲存格式。
    - **INSERT OVERWRITE ...選取** -選取資料列從 **log4jLogs** 包含 [ERROR]，然後再將資料插入 **errorLogs** 資料表。
    
    使用 __Execute__ 按鈕執行這個查詢。  __結果__ ] 索引標籤不會包含任何資訊，此查詢會不傳回任何資料列，但是狀態應顯示為 __成功__。

###Hive 設定

選取 __設定__ 右邊的編輯器] 中的圖示。

![圖示](./media/hdinsight-hadoop-use-hive-ambari-view/settings.png)

設定可以用來變更各種 Hive 設定，例如將 Hive 的執行引擎從 Tez (預設) 變更為 MapReduce。

###視覺解說

選取 __Visual 說明__ 右邊的編輯器] 中的圖示。

![圖示](./media/hdinsight-hadoop-use-hive-ambari-view/visualexplainicon.png)
    
這是 __Visual 說明__ 能幫助您了解複雜的查詢流量在查詢的檢視。 您可以藉由檢視這份檢視的文字對等 __解釋__ 按鈕在 [查詢編輯器] 中。

![視覺解說影像](./media/hdinsight-hadoop-use-hive-ambari-view/visualexplain.png)

###Tez

選取 __Visual 說明__ 右邊的編輯器] 中的圖示。

![圖示](./media/hdinsight-hadoop-use-hive-ambari-view/tez.png)

如果有的話，這會顯示 Tez 針對此查詢使用的定向非循環圖 (DAG)。 如果您想要檢視 DAG 查詢您已執行過，使用 __Tez 檢視__ 改。

###通知

選取 __Visual 說明__ 右邊的編輯器] 中的圖示。

![圖示](./media/hdinsight-hadoop-use-hive-ambari-view/notifications.png)

通知是在執行查詢時產生的訊息。 例如，您會在提交查詢時或發生錯誤時收到通知。

##儲存的查詢

1. 從 [查詢編輯器] 建立新的工作表，並輸入下列查詢：

        SELECT * from errorLogs;
    
    執行查詢以驗證它是否能正常運作。 結果會如下所示：

        errorlogs.t1    errorlogs.t2    errorlogs.t3    errorlogs.t4    errorlogs.t5    errorlogs.t6    errorlogs.t7
        2012-02-03  18:35:34    SampleClass0    [ERROR]     incorrect   id  
        2012-02-03  18:55:54    SampleClass1    [ERROR]     incorrect   id  
        2012-02-03  19:25:27    SampleClass4    [ERROR]     incorrect   id

2. 使用 __存__ 底部的編輯器] 中的按鈕。 這項查詢 __Errorlogs__ ，然後選取 __確定__。 請注意，工作表的名稱會變更為 __Errorlogs__。
    
3. 選取 __儲存查詢__ Hive 檢視頁面頂端的索引標籤。 請注意， __Errorlogs__ 現在會列為已儲存的查詢。 它會保留在此清單中，直到您移除它。 選取名稱，將會在 [查詢編輯器] 中開啟查詢。

##查詢歷程記錄

 __記錄__ hive 控制檔] 檢視頂端的按鈕可讓您檢視您先前執行的查詢。 現在使用它並且選取您先前執行的一些查詢。 當您選取查詢時，它會在 [查詢編輯器] 中開啟。

##使用者定義函數 (UDF)

Hive 也可以透過擴充 **使用者定義函數 (UDF)**。 UDF 可讓您在 HiveQL 中實作功能或不易模型化的邏輯。 

雖然您可以增加 UDF HiveQL 陳述式的一部分，在查詢中，[UDF] 索引標籤頂端的 [hive 控制檔] 檢視可讓您宣告並儲存一組可以搭配使用的 Udf __查詢編輯器__。

若要 hive 控制檔] 檢視中，新增 UDF 之後 __插入 udf__ 按鈕將會出現在底端 __查詢編輯器__。 選取這個項目會顯示在 [Hive 檢視] 中定義的 UDF 的下拉式清單。 選取 UDF 會將 HiveQL 陳述式新增至查詢以啟用 UDF。

例如，如果您使用下列屬性定義 UDF：

* 資源名稱：myudfs
* 資源路徑: wasb:///myudfs.jar
* UDF 名稱：myawesomeudf
* UDF 類別名稱：com.myudfs.Awesome
    
使用 __插入 udf__ 按鈕會顯示項目名稱 __myudfs__, ，與另一個下拉式清單的每個 UDF 定義該資源。 在此情況下， __myawesomeudf__。 選取此項目會將下列項目新增至查詢的開頭：

    add jar wasb:///myudfs.jar;

    create temporary function myawesomeudf as 'com.myudfs.Awesome';

您接著可以在查詢中使用 UDF。 例如，`SELECT myawesomeudf(name) FROM people;`。

如需在 HDInsight 上搭配 Hive 使用 UDF 的詳細資訊，請參閱下列文章：

* [在 HDInsight 中搭配 Hive 與 Pig 使用 Python](hdinsight-python.md)

* [如何將自訂 Hive UDF 新增至 HDInsight (英文)](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

##<a id="nextsteps"></a>後續步驟

如需 HDInsight 中 Hive 的一般資訊：

* [搭配使用 Hive 與 HDInsight 上的 Hadoop](hdinsight-use-hive.md)

如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊：

* [搭配使用 Pig 與 HDInsight 上的 Hadoop](hdinsight-use-pig.md)

* [搭配使用 MapReduce 與 HDInsight 上的 Hadoop](hdinsight-use-mapreduce.md)
