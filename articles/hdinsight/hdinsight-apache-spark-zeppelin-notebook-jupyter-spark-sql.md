<properties
    pageTitle="在 Azure HDInsight 上佈建 Spark 叢集，並使用 Spark SQL 從 Zeppelin 和 Jupyter 執行互動式分析 | Microsoft Azure"
    description="說明如何在 HDInsight 中快速佈建 Apache Spark 叢集，接著使用 Spark SQL 從 Zeppelin 和 Jupyter Notebook 執行互動式查詢的逐步指示。"
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="paulettm"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2015"
    ms.author="nitinme"/>


# 開始使用：在 Azure HDInsight 上佈建 Apache Spark 並使用 Spark SQL 執行互動式查詢

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [在 HDInsight 上佈建 Apache Spark 並使用 Spark SQL 執行互動式查詢](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1.md)

了解如何佈建使用快速建立] 選項在 HDInsight 中的 Apache Spark 叢集，然後使用 [以 web 為基礎 [Zeppelin](https://zeppelin.incubator.apache.org) 和 [Jupyter](https://jupyter.org) notebook 在 Spark 叢集上執行 Spark SQL 互動式查詢。


   ![開始使用 HDInsight 中的 Apache Spark](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.GetStartedFlow.Spark.png  "Get started using Apache Spark in HDInsight tutorial. Steps illustrated: create a storage account; provision a cluster; run Spark SQL statements")

**必要條件：**

開始進行本教學課程之前，您必須擁有 Azure 訂用帳戶。 請參閱 [取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。


## <a name="provision"></a>佈建 HDInsight Spark 叢集

在本節中，您將佈建採用 Spark 1.3.1 版的 HDInsight 3.2 版叢集。 HDInsight 版本及其 Sla 的相關資訊，請參閱 [HDInsight 元件版本設定](hdinsight-component-versioning.md)。

>[AZURE.NOTE] 這篇文章中的步驟使用基本組態設定，在 HDInsight 中建立的 Apache Spark 叢集。 （例如使用 Hive 的其他儲存體、 Azure 虛擬網路或中繼存放區） 的其他叢集組態設定的相關資訊，請參閱 [使用自訂選項佈建 HDInsight 叢集](hdinsight-apache-spark-provision-clusters.md)。


**佈建 Spark 叢集**

1. 登入 [Azure 入口網站](https://ms.portal.azure.com/)。

2. 按一下 [ **新增**, ，按一下 [ **資料 + 分析**, ，然後按一下 [ **HDInsight**。

    ![在 Azure 入口網站中建立新的叢集](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.CreateCluster.1.png "Creating a new cluster in the Azure Portal")

3. 輸入 **叢集名稱**, ，請選取 **Hadoop** 的 **叢集類型**, ，以及從 **叢集作業系統** 下拉式選單中，選取 **Windows Server 2012 R2 Datacenter**。 如果該叢集可用，叢集名稱旁就會出現綠色勾號。

    ![輸入叢集名稱和類型](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.CreateCluster.2.png "Enter cluster name and type")

4. 如果您有一個以上的訂閱，請按一下 [ **訂閱** 項目來選擇要在叢集中使用的 Azure 訂閱。

5. 按一下 [ **資源群組** 查看現有的資源群組的清單，並選取要建立叢集的位置。 或者，您可以按一下 **新建** ，然後輸入新的資源群組的名稱。 出現綠色勾號即表示新群組的名稱可供使用。

    > [AZURE.NOTE] 如果有的話，這個項目會預設為其中一個現有的資源群組。

6. 按一下 [ **認證**, ，然後輸入 **叢集登入使用者名稱** 和 **叢集登入密碼**。 如果您想要為在叢集節點上，啟用遠端桌面 **啟用遠端桌面**, ，按一下 [ **是**, ，然後指定所需的值。 本教學課程並不需要遠端桌面，因此您可以略過這個步驟。 按一下 [ **選取** 底部要儲存認證的設定。

    ![提供叢集認證](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.CreateCluster.3.png "Provide cluster credentials")

7. 按一下 [ **資料來源** 選擇現有的資料來源的叢集，或建立一個新。 當您在 HDInsight 中佈建 Hadoop 叢集時，您需要指定一個 Azure 儲存體帳戶。 該帳戶特定的 Blob 儲存體容器將被指定為預設檔案系統，如同 Hadoop 分散式檔案系統 (HDFS)。 依預設，系統會在與您指定儲存體帳戶的相同資料中心內佈建 HDInsight 叢集。 如需詳細資訊，請參閱 [搭配 HDInsight 使用 Azure Blob 儲存體][hdinsight-storage]

    ![資料來源刀鋒視窗](./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.CreateCluster.4.png "Provide data source configuration")

    目前您可以選取 Azure 儲存體帳戶做為 HDInsight 叢集資料來源。 了解這些項目上使用下列 **資料來源** 刀鋒視窗。

    - **選取方法**︰ 設為 **從所有的訂閱** 才能瀏覽儲存體帳戶從您的訂閱。 請設為 **便捷鍵** 如果您想要輸入 **儲存體名稱** 和 **便捷鍵** 現有的儲存體帳戶。

    - **選取儲存體帳戶建立新的 /**︰ 按一下 **選取儲存體帳戶** 瀏覽並選取您要與叢集相關聯的現有儲存體帳戶。 或者，按一下 [ **新建** 來建立新的儲存體帳戶。 使用出現的欄位輸入儲存體帳戶名稱。 如果該名稱可供使用，就會出現綠色勾號。

    - **選擇預設容器**︰ 使用此選項可輸入要用於叢集的預設容器名稱。 雖然您可以輸入任何名稱，但我們建議您使用與叢集相同的名稱，以便輕易辨識用於這個特定叢集的容器。

    - **位置**︰ 儲存體帳戶，或將中建立的地理區域。

        > [AZURE.IMPORTANT] 選取的預設資料來源的位置也會將設定 HDInsight 叢集的位置。 叢集和預設資料來源必須位於相同區域中。

    按一下 [ **選取** 儲存資料來源設定。

8. 按一下 [ **節點定價層** 顯示將建立此叢集節點的相關資訊。 設定該叢集所需的背景工作角色節點數目。 該叢集的預估成本將會顯示在此刀鋒視窗內。

    ![節點定價層刀鋒視窗](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.CreateCluster.5.png "Specify number of cluster nodes")

    按一下 [ **選取** 儲存定價組態的節點。

9. 在 **新的 HDInsight 叢集** 刀鋒視窗中，確定 **釘選到開始面板** 已選取，然後按一下 [ **建立**。 這會建立叢集，並將該叢集磚加入到您 Azure 入口網站的開始面板。 該圖示可表示該叢集正在佈建，並將在佈建完成後變更為 HDInsight 圖示。

  	| 佈建期間 | 佈建完成 |
  	| ------------------ | --------------------- |
  	| ![「開始面板」上的佈建指示器](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/provisioning.png) | ![佈建的叢集磚](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/provisioned.png) |

    > [AZURE.NOTE] 需要花一些時間，叢集才會建立，通常約 15 分鐘的時間。 在開始面板中，使用 [] 磚或 **通知** 佈建程序檢查頁面左邊的項目。

10. 佈建完成後，在開始面板上按一下 Spark 叢集的磚，以啟動叢集刀鋒視窗。


## <a name="zeppelin"></a>使用 Zeppelin Notebook 執行互動式 Spark SQL 查詢

佈建叢集後，您可以使用網頁型的 Zeppelin Notebook 來針對 Spark HDInsight 叢集執行 Spark SQL 互動式查詢。 在本節中，我們將使用叢集預設提供的範例資料檔案 (hvac.csv) 來執行一些互動式 Spark SQL 查詢。

>[AZURE.NOTE] 也會在叢集上的預設可以遵循下列指示建立的 notebook。 啟動 Zeppelin 後，您將會依名稱找到本 notebook **Zeppelin HVAC tutorial**。

1. 從 [Azure 入口網站](https://portal.azure.com/), ，從 [開始面板] 中，按一下 [Spark 叢集的磚 （如果固定至開始面板）。 您也可以導覽至您的叢集下 **全部瀏覽** > **HDInsight 叢集**。   

2. 從 Spark 叢集刀鋒視窗中，按一下 [ **快速連結**, ，然後從 **叢集儀表板** 刀鋒視窗中，按一下 [ **Zeppelin Notebook**。 出現提示時，輸入叢集的系統管理員認證。

    > [AZURE.NOTE] 您也可能在您的瀏覽器中開啟下列 URL，為您的叢集到達 Zeppelin Notebook。 取代 __CLUSTERNAME__ 與您的叢集名稱 ︰
    >
    > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. 建立新的 Notebook。 從標頭窗格中，按一下 **筆記本**, ，然後按一下 [ **建立新 Note**。

    ![建立新的 Zeppelin Notebook](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.CreateNewNote.png "Create a new Zeppelin notebook")

    在相同頁面上，在 **筆記本** 標題時，您應該會看到名稱開頭為新筆記型電腦 **Note XXXXXXXXX**。 按一下新的 Notebook。

3. 在新 Notebook 的網頁上按一下標題，並視需要變更 Notebook 的名稱。 按下 ENTER 以儲存名稱變更。 此外，請確定 notebook 標頭會顯示 **連線** 右上角中的狀態。

    ![Zeppelin Notebook 狀態](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.NewNote.Connected.png "Zeppelin notebook status")

4. 將範例資料載入暫存資料表。 當您佈建 Spark 叢集，在 HDInsight 中，範例資料檔 **hvac.csv**, ，複製到相關聯的儲存體帳戶 **\HdiSamples\SensorSampleData\hvac**。

    將以下程式碼貼入新 Notebook 中預設建立的空白段落：

        // Create an RDD using the default Spark context, sc
        val hvacText = sc.textFile("wasb:///HdiSamples/SensorSampleData/hvac/HVAC.csv")

        // Define a schema
        case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)

        // Map the values in the .csv file to the schema
        val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
            s => Hvac(s(0),
                    s(1),
                    s(2).toInt,
                    s(3).toInt,
                    s(6)
            )
        ).toDF()

        // Register as a temporary table called "hvac"
        hvac.registerTempTable("hvac")

    按下 **SHIFT + ENTER** 在鍵盤上按 **播放** 按鈕讓段落執行程式碼。 段落右上角的狀態應該會從「準備就緒」逐一轉變成「擱置」、「執行中」及「已完成」。 輸出會出現在同一個段落的底部。 螢幕擷取畫面如下所示：

    ![從原始資料建立暫存資料表](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Note.LoadDataIntoTable.png "Create a temporary table from raw data")

    您也可以為每個段落提供標題。 在右下角，按一下 [ **設定** 圖示，然後再按一下 **顯示標題**。

5. 您現在可以在執行 Spark SQL 陳述式 **hvac** 資料表。 將以下查詢貼入新段落。 此查詢會擷取建築物識別碼，以及在指定日期當天每棟建築物之目標溫度與實際溫度間的差異。 按下 **SHIFT + ENTER**。

        %sql
        select buildingID, (targettemp - actualtemp) as temp_diff, date
        from hvac
        where date = "6/1/13"

     **%Sql** 開頭的陳述式會告訴 notebook 使用 Spark SQL 解譯器。 您可以查看定義的解譯器，從 **直譯器** notebook 標頭中的索引標籤。

    以下螢幕擷取畫面顯示輸出。

    ![使用 Notebook 執行 Spark SQL 陳述式](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Note.SparkSQLQuery1.png "Run a Spark SQL statement using the notebook")

    按一下顯示選項 (以矩形反白顯示) 以針對相同輸出切換不同的表示法。 按一下 [ **設定** 以選擇構成索引鍵和值在輸出中。 上述螢幕擷取畫面使用 **buildingID** 做為索引鍵和的平均值 **temp_diff** 做為值。

6. 您也可以在查詢中使用變數來執行 Spark SQL 陳述式。 下一步的程式碼範例示範如何定義變數， **Temp**, ，在您想要使用查詢的可能值的查詢。 當您第一次執行查詢時，下拉式清單會自動填入您指定的變數值。

        %sql
        select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff
        from hvac
        where targettemp > "${Temp = 65,65|75|85}"

    這個程式碼範例貼入新段落，然後按下 **SHIFT + ENTER**。 以下螢幕擷取畫面顯示輸出。

    ![使用 Notebook 執行 Spark SQL 陳述式](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Note.SparkSQLQuery2.png "Run a Spark SQL statement using the notebook")

    對於後續的查詢，您可以從下拉式清單選取新的值，然後再次執行查詢。 按一下 [ **設定** 以選擇構成索引鍵和值在輸出中。 上述螢幕擷取畫面使用 **buildingID** 做為索引鍵，平均 **temp_diff** 的值，和 **targettemp** 做為群組。

7. 重新啟動 Spark SQL 解譯器以結束應用程式。 按一下 [ **直譯器** 的頂端，然後針對 Spark 解譯索引標籤上，按一下 **重新啟動**。

    ![重新啟動 Zeppelin 解譯器](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Zeppelin.Restart.Interpreter.png "Restart the Zeppelin intepreter")

## <a name="jupyter"></a>使用 Jupyter Notebook 執行 Spark SQL 查詢

在本節中，您會使用 Jupyter Notebook 來針對 Spark 叢集執行 Spark SQL 查詢。

>[AZURE.NOTE] 也會在叢集上的預設可以遵循下列指示建立的 notebook。 啟動 Jupyter 後，您將會依名稱找到本 notebook **HVACTutorial.ipynb**。

1. 從 [Azure 入口網站](https://portal.azure.com/), ，從 [開始面板] 中，按一下 [Spark 叢集的磚 （如果固定至開始面板）。 您也可以導覽至您的叢集下 **全部瀏覽** > **HDInsight 叢集**。   

2. 從 Spark 叢集刀鋒視窗中，按一下 [ **快速連結**, ，然後從 **叢集儀表板** 刀鋒視窗中，按一下 [ **Jupyter Notebook**。 出現提示時，輸入叢集的系統管理員認證。

    > [AZURE.NOTE] 您也可能在您的瀏覽器中開啟下列 URL，為您的叢集到達 Jupyter Notebook。 取代 __CLUSTERNAME__ 與您的叢集名稱 ︰
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. 建立新的 Notebook。 按一下 [ **新增**, ，然後按一下 [ **Python2**。

    ![建立新的 Jupyter Notebook](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Note.Jupyter.CreateNotebook.png "Create a new Jupyter notebook")

3. 系統隨即會建立新 Notebook，並以 Untitled.pynb 的名稱開啟。 在頂端按一下 Notebook 名稱，然後輸入好記的名稱。

    ![提供 Notebook 的名稱](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Note.Jupyter.Notebook.Name.png "Provide a name for the notebook")

4. 匯入所需的模組，然後建立 Spark 和 SQL 內容。 下列程式碼範例貼入空白儲存格，然後按 **SHIFT + ENTER**。

        from pyspark import SparkContext
        from pyspark.sql import SQLContext
        from pyspark.sql.types import *

        # Create Spark and SQL contexts
        sc = SparkContext('spark://headnodehost:7077', 'pyspark')
        sqlContext = SQLContext(sc)

    每次您在 Jupyter 中執行工作時，網頁瀏覽器視窗標題會顯示 **（忙碌）** notebook 標題和狀態。 您也會旁看到實心圓 **Python 2** 右上角中的文字。 工作完成後，實心圓將變成空心圓。

     ![Jupyter Notebook 工作的狀態](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Jupyter.Job.Status.png "Status of a Jupyter notebook job")

4. 將範例資料載入暫存資料表。 當您佈建 Spark 叢集，在 HDInsight 中，範例資料檔 **hvac.csv**, ，複製到相關聯的儲存體帳戶 **\HdiSamples\SensorSampleData\hvac**。

    空白儲存格，貼上下列程式碼範例，請按 **SHIFT + ENTER**。 這個程式碼範例會將資料註冊到暫存資料表，稱為 **hvac**。

        # Load the data
        hvacText = sc.textFile("wasb:///HdiSamples/SensorSampleData/hvac/HVAC.csv")

        # Create the schema
        hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])

        # Parse the data in hvacText
        hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))

        # Create a data frame
        hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

        # Register the data fram as a table to run queries against
        hvacdf.registerAsTable("hvac")

        # Run queries against the table and display the data
        data = sqlContext.sql("select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = \"6/1/13\"")
        data.show()

5. 待工作順利完成後，即會顯示以下輸出。

        buildingID temp_diff date  
        4          8         6/1/13
        3          2         6/1/13
        7          -10       6/1/13
        12         3         6/1/13
        7          9         6/1/13
        7          5         6/1/13
        3          11        6/1/13
        8          -7        6/1/13
        17         14        6/1/13
        16         -3        6/1/13
        8          -8        6/1/13
        1          -1        6/1/13
        12         11        6/1/13
        3          14        6/1/13
        6          -4        6/1/13
        1          4         6/1/13
        19         4         6/1/13
        19         12        6/1/13
        9          -9        6/1/13
        15         -10       6/1/13

6. 重新啟動核心以結束應用程式。 從頂端功能表列上，按一下 [ **核心**, ，按一下 [ **重新啟動**, ，然後按一下 [ **重新啟動** 提示字元。

    ![重新啟動 Jupyter 核心](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Jupyter.Restart.Kernel.png "Restart the Jupyter Kernel")


## <a name="seealso"></a>另請參閱


* [概觀：Azure HDInsight 上的 Apache Spark](hdinsight-apache-spark-overview.md)
* [在 HDInsight 叢集上佈建 Spark](hdinsight-apache-spark-provision-clusters.md)
* [執行 HDInsight 中使用 Spark 與 BI 工具的互動式資料分析](hdinsight-apache-spark-use-bi-tools.md)
* [在 HDInsight 中使用 Spark 建置機器學習應用程式](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [在 HDInsight 中使用 Spark 建置即時串流應用程式](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)
* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](hdinsight-apache-spark-resource-manager.md)


[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/


