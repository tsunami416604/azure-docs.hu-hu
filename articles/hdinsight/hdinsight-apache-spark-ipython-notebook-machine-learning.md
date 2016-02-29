<properties 
    pageTitle="在 HDInsight 上使用 Apache Spark 建置機器學習應用程式 | Microsoft Azure" 
    description="說明如何搭配使用 Notebook 和 Apache Spark 來建置機器學習應用程式的逐步指示" 
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


# 在 Azure HDInsight 上使用 Apache Spark 建置機器學習服務應用程式

了解如何在 HDInsight 中使用 Apache Spark 叢集建置機器學習應用程式。 本文說明如何使用叢集隨附的 Jupyter Notebook 來建置及測試應用程式。 應用程式使用所有叢集預設提供的範例 HVAC.csv 資料。

**必要條件：**

您必須滿足以下條件：

- Azure 訂用帳戶。 請參閱 [取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- Apache Spark 叢集。 如需指示，請參閱 [佈建 Apache Spark 叢集的 Azure HDInsight](hdinsight-apache-spark-provision-clusters.md)。 

##<a name="data"></a>顯示資料

在開始建置應用程式之前，我們先來了解資料的結構，以及要針對資料執行哪種分析。 

在本文中，我們使用範例 **HVAC.csv** 可在所有 HDInsight 叢集的預設位置取得的資料檔 **\HdiSamples\SensorSampleData\hvac**。 下載及開啟 CSV 檔案，以取得資料的快照。  

![HVAC 資料快照](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/HDI.Spark.ML.Show.Data.png "Snapshot of the HVAC data")

這項資料會顯示安裝 HVAC 系統之建築物的目標溫度和實際溫度。 讓我們假設 **系統** 資料行代表系統識別碼和 **SystemAge** 資料行代表 HVAC 系統已在建築物中的年數。

在指定系統識別碼和系統年期的情況下，我們可以使用這些資料來預測建築物的溫度會比目標溫度高或低。

##<a name="app"></a>撰寫使用 Spark MLlib 的機器學習應用程式

1. 從 [Azure 入口網站](https://portal.azure.com/), ，從 [開始面板] 中，按一下 [Spark 叢集的磚 (如果固定至開始面板)。 您也可以導覽至您的叢集下 **全部瀏覽** > **HDInsight 叢集**。   

2. 從 Spark 叢集刀鋒視窗中，按一下 [ **快速連結**, ，然後從 **叢集儀表板** 刀鋒視窗中，按一下 [ **Jupyter Notebook**。 出現提示時，輸入叢集的系統管理員認證。

    > [AZURE.NOTE] 您也可能在您的瀏覽器中開啟下列 URL，為您的叢集到達 Jupyter Notebook。 取代 __CLUSTERNAME__ 與您的叢集名稱:
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. 建立新的 Notebook。 按一下 [ **新增**, ，然後按一下 [ **Python 2**。

    ![建立新的 Jupyter Notebook](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/HDI.Spark.Note.Jupyter.CreateNotebook.png "Create a new Jupyter notebook")

3. 系統隨即會建立新 Notebook，並以 Untitled.pynb 的名稱開啟。 在頂端按一下 Notebook 名稱，然後輸入好記的名稱。

    ![提供 Notebook 的名稱](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/HDI.Spark.Note.Jupyter.Notebook.Name.png "Provide a name for the notebook")

3. 開始建置機器學習服務應用程式。 在此應用程式中，我們會使用 Spark ML 管線來執行文件分類。 在管線中，我們將文件分割成單字、將單字轉換成數值特性向量，最後再使用特性向量和標籤建立預測模型。

    若要開始建置應用程式，您需要先匯入所需的模組，並將資源指派給應用程式。 新 notebook 中的空白儲存格，貼上下列程式碼片段，然後按下 **SHIFT + ENTER**。


        from pyspark.ml import Pipeline
        from pyspark.ml.classification import LogisticRegression
        from pyspark.ml.feature import HashingTF, Tokenizer
        from pyspark.sql import Row, SQLContext
        
        import os
        import sys
        from pyspark import SparkConf
        from pyspark import SparkContext
        from pyspark.sql import SQLContext
        from pyspark.sql.types import *
        
        from pyspark.mllib.classification import LogisticRegressionWithSGD
        from pyspark.mllib.regression import LabeledPoint
        from numpy import array
        
        # Assign resources to the application
        conf = SparkConf()
        conf.setMaster('spark://headnodehost:7077')
        conf.setAppName('pysparkregression')
        conf.set("spark.cores.max", "4")
        conf.set("spark.executor.memory", "4g")
        
        sc = SparkContext(conf=conf)
        sqlContext = SQLContext(sc)

    Everytime you run a job in Jupyter, your web browser window title will show a **(Busy)** status along with the notebook title. You will also see a solid circle next to the **Python 2** text in the top-right corner. After the job completes, this will change to a hollow circle.

     ![Status of a Jupyter notebook job](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/HDI.Spark.Jupyter.Job.Status.png "Status of a Jupyter notebook job")
 
4. 您現在必須載入資料 (hvac.csv)、剖析資料，以及利用它來為模型定型。 為此，您需要定義檢查建築物之實際溫度是否高於目標溫度的函示。 如果實際溫度較大，建築物處於作用，此值以表示 **1.0**。 如果實際溫度是較低，建築物處於低溫狀態，表示值 **0.0**。 

    下列程式碼片段貼入空白儲存格，然後按下 **SHIFT + ENTER**。

        
        # List the structure of data for better understanding. Becuase the data will be
        # loaded as an array, this structure makes it easy to understand what each element
        # in the array corresponds to

        # 0 Date
        # 1 Time
        # 2 TargetTemp
        # 3 ActualTemp
        # 4 System
        # 5 SystemAge
        # 6 BuildingID

        LabeledDocument = Row("BuildingID", "SystemInfo", "label")

        # Define a function that parses the raw CSV file and returns an object of type LabeledDocument
        
        def parseDocument(line):
            values = [str(x) for x in line.split(',')]
            if (values[3] > values[2]):
                hot = 1.0
            else:
                hot = 0.0        
    
            textValue = str(values[4]) + " " + str(values[5])
    
            return LabeledDocument((values[6]), textValue, hot)

        # Load the raw HVAC.csv file, parse it using the function
        data = sc.textFile("wasb:///HdiSamples/SensorSampleData/hvac/HVAC.csv")

        documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
        training = documents.toDF()


5. 設定包含三個階段的 Spark 機器學習管線：tokenizer、hashingTF 及 lr。 如需有關什麼是管線及其運作方式請參閱 <a href="http://spark.apache.org/docs/latest/ml-guide.html#how-it-works" target="_blank">Spark 機器學習管線</a>.

    下列程式碼片段貼入空白儲存格，然後按下 **SHIFT + ENTER**。

        tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
        hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
        lr = LogisticRegression(maxIter=10, regParam=0.01)
        pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

6. 讓管線符合訓練文件。 下列程式碼片段貼入空白儲存格，然後按下 **SHIFT + ENTER**。

        model = pipeline.fit(training)

7. 驗證訓練文件以根據應用程式的進度設立檢查點。 下列程式碼片段貼入空白儲存格，然後按下 **SHIFT + ENTER**。

        training.show()

    如此應該會產生如下所示的輸出：

        BuildingID SystemInfo label
        4          13 20      0.0  
        17         3 20       0.0  
        18         17 20      1.0  
        15         2 23       0.0  
        3          16 9       1.0  
        4          13 28      0.0  
        2          12 24      0.0  
        16         20 26      1.0  
        9          16 9       1.0  
        12         6 5        0.0  
        15         10 17      1.0  
        7          2 11       0.0  
        15         14 2       1.0  
        6          3 2        0.0  
        20         19 22      0.0  
        8          19 11      0.0  
        6          15 7       0.0  
        13         12 5       0.0  
        4          8 22       0.0  
        7          17 5       0.0

    返回並根據原始 CSV 檔案驗證輸出。 例如，CSV 檔案中第一個資料列的資料為：

    ![HVAC 資料快照](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/HDI.Spark.ML.Show.Data.First.Row.png "Snapshot of the HVAC data")

    請注意，實際溫度比目標溫度低的情況代表建築物處於低溫狀態。 因此在訓練輸出的值 **標籤** 在第一個資料列是 **0.0**, ，表示建築物並非處於高溫狀態。

8.  準備要做為定型模型之執行依據的資料集。 若要這樣做，我們會傳送系統識別碼和系統存留期 (表示為 **簡明** 訓練輸出中)，和模型會預測該系統識別碼且年期建置是否會有更高 (以 1.0 表示) 或更冷色 (以 0.0 表示)。

    下列程式碼片段貼入空白儲存格，然後按下 **SHIFT + ENTER**。
        
        # SystemInfo here is a combination of system ID followed by system age
        Document = Row("id", "SystemInfo")
        test = sc.parallelize([(1L, "20 25"),
                      (2L, "4 15"),
                      (3L, "16 9"),
                      (4L, "9 22"),
                      (5L, "17 10"),
                      (6L, "7 22")]) \
            .map(lambda x: Document(*x)).toDF() 

9. 最後，根據測試資料進行預測。 下列程式碼片段貼入空白儲存格，然後按下 **SHIFT + ENTER**。

        # Make predictions on test documents and print columns of interest
        prediction = model.transform(test)
        selected = prediction.select("SystemInfo", "prediction", "probability")
        for row in selected.collect():
            print row

10. 您應該會看到如下所示的輸出：

        Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
        Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
        Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
        Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
        Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
        Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))

    從預測中的第一個資料列，您可以看到對於識別碼為 20 且年期為 25 年的 HVAC 系統，建築物將處於作用 (**預測 = 1.0**)。 DenseVector (0.49999) 的第一個值對應到預測 0.0，第二個值 (0.5001) 對應到預測 1.0。 在輸出中，即使第二個值只是稍微更高版本，模型仍舊顯示 **預測 = 1.0**。

11. 您現在可以重新啟動核心以結束 Notebook。 從頂端功能表列上，按一下 [ **核心**, ，按一下 [ **重新啟動**, ，然後按一下 [ **重新啟動** 提示字元。

    ![重新啟動 Jupyter 核心](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/HDI.Spark.Jupyter.Restart.Kernel.png "Restart the Jupyter Kernel")
           

##<a name="anaconda"></a>使用 Anaconda scikit-learn-Machine Learning 的學習程式庫

HDInsight 上的 Apache Spark 叢集包含 Anaconda 程式庫。 這也包括 **scikit-learn-了解** 機器學習程式庫。 此程式庫另包含用來直接從 Jupyter Notebook 建置範例應用程式的各種資料集。 如需使用 scikit-learn 範例-學習程式庫，請參閱 [http://scikit-learn.org/stable/auto_examples/index.html](http://scikit-learn.org/stable/auto_examples/index.html)。

##<a name="seealso"></a>另請參閱

* [概觀：Azure HDInsight 上的 Apache Spark](hdinsight-apache-spark-overview.md)
* [在 HDInsight 叢集上佈建 Spark](hdinsight-apache-spark-provision-clusters.md)
* [在 HDInsight 中搭配使用 Spark 和 BI 工具執行互動式資料分析](hdinsight-apache-spark-use-bi-tools.md)
* [在 HDInsight 中使用 Spark 建置即時串流應用程式](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)
* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](hdinsight-apache-spark-resource-manager.md)


[hdinsight-versions]: ../hdinsight-component-versioning/

[hdinsight-upload-data]: ../hdinsight-upload-data/

[hdinsight-storage]: ../hdinsight-use-blob-storage/


[hdinsight-weblogs-sample]: ../hdinsight-hive-analyze-website-log/
[hdinsight-sensor-data-sample]: ../hdinsight-hive-analyze-sensor-data/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

