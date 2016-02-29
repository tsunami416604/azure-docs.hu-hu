<properties
    pageTitle="在 HDInsight 中搭配 Hive 與 Pig 來使用 Python | Microsoft Azure"
    description="了解如何在 HDInsight 中從 Hive 和 Pig (Azure 上的 Hadoop 技術堆疊) 使用 Python 使用者定義函數 (UDF)。"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="paulettm"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="12/04/2015" 
    ms.author="larryfr"/>

#在 HDInsight 中搭配 Hive 與 Pig 來使用 Python

Hive 與 Pig 很適合在 HDInsight 中處理資料，但您有時需要更通用的語言。 Hive 與 Pig 都可讓您使用各種程式設計語言來建立使用者定義函數 (UDF)。 在本文中，您將了解如何從 Hive 和 Pig 中使用 Python UDF。

> [AZURE.NOTE] 這篇文章中的步驟適用於 HDInsight 叢集 2.1、 3.0、 3.1 和 3.2 版。


##<a name="python"></a>HDInsight 上的 Python

HDInsight 3.0 及更新版本的叢集上預設為已安裝 Python2.7。 Hive 可以搭配此版本的 Python 來進行串流處理 (Hive 與 Python 之間使用 STDOUT/STDIN 傳遞資料)。

HDInsight 也包含 Jython (以 Java 撰寫的 Python 實作)。 Pig 知道如何與 Jython 互動，而不必依賴串流，所以使用 Pig 時很適合。

###<a name="hivepython"></a>Hive 與 Python

Python 可以當做從 Hive 透過 HiveQL UDF **轉換** 陳述式。 例如，下列 HiveQL 會叫用 Python 指令碼儲存在 **streaming.py** 檔案。

**以 Linux 為基礎的 HDInsight**

    add file wasb:///streaming.py;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
      USING 'streaming.py' AS
      (clientid string, phoneLable string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;

**以 Windows 為基礎的 HDInsight**

    add file wasb:///streaming.py;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
      USING 'D:\Python27\python.exe streaming.py' AS
      (clientid string, phoneLable string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;

> [AZURE.NOTE] 在 Windows 為基礎的 HDInsight 叢集上 **使用** 子句必須指定至 python.exe 的完整路徑。 這一律為 `D:\Python27\python.exe`

以下是此範例所執行的動作：

1.  **將檔案加入** 檔案的開頭的陳述式加入 **streaming.py** 分散式快取，供叢集中的所有節點存取的檔案。

2.   **SELECT TRANSFORM...使用** 陳述式選取資料 **hivesampletable**, ，並將 clientid、 devicemake 和 devicemodel 傳遞至 **streaming.py** 指令碼。

3.  **AS** 子句描述傳回的欄位 **streaming.py**

<a name="streamingpy"></a>
以下是 **streaming.py** HiveQL 範例使用檔案。

    #!/usr/bin/env python

    import sys
    import string
    import hashlib

    while True:
      line = sys.stdin.readline()
      if not line:
        break

      line = string.strip(line, "\n ")
      clientid, devicemake, devicemodel = string.split(line, "\t")
      phone_label = devicemake + ' ' + devicemodel
      print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])

因為我們使用串流，此指令碼必須執行下列動作：

1. 從 STDIN 中讀取資料。 在此範例中，使用 `sys.stdin.readline()` 來完成。

2. 使用 `string.strip(line, "\n ")` 移除結尾新行字元，因為只想要文字資料而非行尾指標。

2. 執行串流處理時，有一行包含所有的值，而每個值之間是一個定位字元。 因此，`string.split(line, "\t")` 可在每個索引標籤進行分割輸入，並只傳回欄位。

3. 處理完成時，輸出必須以一行寫入 STDOUT，而每一個欄位之間是一個定位字元。 這是使用  `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])` 以完成。

4. 這全部發生在 `while` 迴圈內，迴圈會一直重複直到沒有 `line` 讀取為止，此時`break` 會結束迴圈，指令碼也就終止。

除此之外，指令碼就只是串連 `devicemake` 和 `devicemodel` 的輸入值，並計算串連值的雜湊。 相當簡單，但其描述從 Hive 叫用的任何 Python 指令碼應如何運作的基本知識：迴圈，讀取輸入直到再也沒有輸入為止，然後在索引標籤處中斷每行輸入，接著處理，再寫入索引標籤分隔輸出的單一行。

請參閱 [執行範例](#running) 如何在 HDInsight 叢集上執行這個範例。

###<a name="pigpython"></a>Pig 與 Python

Python 指令碼可用來當做從 Pig 中，透過 UDF **產生** 陳述式。 例如，下列範例會使用儲存在一個 Python 指令碼 **jython.py** 檔案。

    Register 'wasb:///jython.py' using jython as myfuncs;
    LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
    DUMP DETAILS;

以下是此範例的運作情形：

1. 它會註冊含有 Python 指令碼檔案 (**jython.py**,，) 使用 **Jython**, ，並公開給 Pig 做為 **myfuncs**。 Jython 是以 Java 撰寫的 Python 實作，在與 Pig 相同的 Java 虛擬機器中執行。 這樣可讓我們將 Python 指令碼視同傳統的函數呼叫，而不是 Hive 使用的串流方法。

2. 下一行會載入範例資料檔 **sample.log** 到 **記錄**。 因為此記錄檔沒有一致的結構描述，它也會定義每一筆記錄 (**列** 在此情況下，) 做為 **chararray**。 Chararray 基本上就是字串。

3. 第三行濾除任何 null 值，然後儲存到作業的結果 **記錄**。

4. 接下來，逐一中記錄 **記錄** ，並使用 **產生** 來叫用 **myfuncs** 方法中包含 **jython.py** 指令碼以載入 **myfuncs**。  **列** 用來將目前的記錄傳遞至函式。

5. 最後，將輸出傾印至 STDOUT 使用 **傾印** 命令。 這只是為了在操作完成之後, 立即顯示結果在實際的指令碼通常 **存放區** 資料插入新的檔案。

<a name="jythonpy"></a>
以下是 **jython.py** Pig 範例使用檔案:

    @outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
    def create_structure(input):
      if (input.startswith('java.lang.Exception')):
        input = input[21:len(input)] + ' - java.lang.Exception'
      date, time, classname, level, detail = input.split(' ', 4)
      return date, time, classname, level, detail

請記住，我們先前只定義 **列** 輸入為 chararray，因為輸入沒有一致的結構描述? 什麼 **jython.py** 沒有為資料轉換成一致的結構描述的輸出。 運作方式如下：

1.  **@OutputSchema** 陳述式會定義將傳回給 Pig 的資料格式。 在此情況下，它有 **資料包**, ，這是一種 Pig 資料類型。 Bag 包含下列欄位，全部都是 chararray (字串)：

    * date - 記錄項目的建立日期
    * time - 記錄項目的建立時間
    * classname - 建立項目所針對的類別名稱
    * level - 記錄層級
    * detail - 記錄項目的詳細資料

2. 下一步] **def create_structure** 定義 Pig 傳入行項目的函式。

3. 此範例資料， **sample.log**, 、 最符合日期、 時間、 classname、 level 和 detail 我們想要傳回的結構描述。 但它也包含幾行，以字串 '*java.lang.Exception*'，需要修改以符合結構描述。  **如果** 陳述式檢查，然後這輸入的資料來移動 '*java.lang.Exception*' 尾端，使資料符合我們預期的輸出結構描述的字串。

4. 下一步] **分割** 命令用來將資料在第四個空白字元處分割。 這會導致五個值，分別指派給 **日期**, ，**時間**, ，**classname**, ，**層級**, ，和 **詳細**。

5. 最後，將值傳回給 Pig。

當資料傳回給 Pig 時，它會有一致的結構描述中所定義 **@outputSchema** 陳述式。

##<a name="running"></a>執行範例

如果您使用以 Linux 為基礎的 HDInsight 叢集時，使用 **SSH** 下列步驟。 如果您使用以 Windows 為基礎的 HDInsight 叢集和 Windows 用戶端，使用 **PowerShell** 步驟。

###SSH

如需有關如何使用 SSH 的詳細資訊，請參閱 <a href="../hdinsight-hadoop-linux-use-ssh-unix/" target="_blank">從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop</a> 或 <a href="../hdinsight-hadoop-linux-use-ssh-windows/" target="_blank">從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop</a>.

1. 使用 Python 範例 [streaming.py](#streamingpy) 和 [jython.py](#jythonpy), ，建立您的開發電腦上的檔案的本機複本。

2. 使用 `scp` 將檔案複製到您的 HDInsight 叢集。 例如，下列將檔案複製到名為 **mycluster**。

        scp streaming.py jython.py myuser@mycluster-ssh.azurehdinsight.net:

3. 使用 SSH 連接到叢集。 例如，下列會連線至名為 **mycluster** 以使用者身分 **myuser**。

        ssh myuser@mycluster-ssh.azurehdinsight.net

4. 從 SSH 工作階段，將先前上傳的 python 檔案加入叢集的 WASB 儲存體。

        hadoop fs -copyFromLocal streaming.py /streaming.py
        hadoop fs -copyFromLocal jython.py /jython.py

上傳檔案之後，請使用下列步驟執行 Hive 和 Pig 工作。

####Hive

1. 使用 `hive` 命令啟動 Hive Shell。 在 Shell 載入完成時，您應會看到 `hive>` 提示。

2. 在出現 `hive>` 提示時輸入下列內容。

        add file wasb:///streaming.py;
        SELECT TRANSFORM (clientid, devicemake, devicemodel)
          USING 'streaming.py' AS
          (clientid string, phoneLabel string, phoneHash string)
        FROM hivesampletable
        ORDER BY clientid LIMIT 50;

3. 輸入最後一行後，工作應該就會開始。 最後將會傳回類似下列的輸出。

        100041  RIM 9650    d476f3687700442549a83fac4560c51c
        100041  RIM 9650    d476f3687700442549a83fac4560c51c
        100042  Apple iPhone 4.2.x  375ad9a0ddc4351536804f1d5d0ea9b9
        100042  Apple iPhone 4.2.x  375ad9a0ddc4351536804f1d5d0ea9b9
        100042  Apple iPhone 4.2.x  375ad9a0ddc4351536804f1d5d0ea9b9

####Pig

1. 使用 `pig` 命令啟動 Shell。 在 Shell 載入完成時，您應會看到 `grunt>` 提示。

2. 在出現 `grunt>` 提示時輸入下列陳述式。

        Register wasb:///jython.py using jython as myfuncs;
        LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
        DUMP DETAILS;

3. 輸入下列行之後，工作應該就會開始。 最後將會傳回類似下列的輸出。

        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

###PowerShell

這些步驟使用 Azure PowerShell。 如果這是尚未安裝，且您的開發電腦上設定，請參閱 [如何安裝和設定 Azure PowerShell](../install-configure-powershell.md) 之前使用下列步驟。

1. 使用 Python 範例 [streaming.py](#streamingpy) 和 [jython.py](#jythonpy), ，建立您的開發電腦上的檔案的本機複本。

2. 使用下列 PowerShell 指令碼上傳 **streaming.py** 和 **jython.py** 檔案加入至伺服器。 替代的 Azure HDInsight 叢集，以及路徑名稱 **streaming.py** 和 **jython.py** 上的前三行指令碼的檔案。

        $clusterName = YourHDIClusterName
        $pathToStreamingFile = "C:\path\to\streaming.py"
        $pathToJythonFile = "C:\path\to\jython.py"

        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup `
            | %{ $_.Key1 }

        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
        
        Set-AzureStorageBlobContent `
            -File $pathToStreamingFile `
            -Blob "streaming.py" `
            -Container $container `
            -Context $context
        
        Set-AzureStorageBlobContent `
            -File $pathToJythonFile `
            -Blob "jython.py" `
            -Container $container `
            -Context $context

    此指令碼會擷取 HDInsight 叢集的資訊，然後擷取預設儲存體帳戶的帳戶和金鑰，再將檔案上傳至容器的根目錄。

    > [AZURE.NOTE] 上傳指令碼的其他方法可在 [hdinsight 的資料上傳](hdinsight-upload-data.md) 文件。

上傳檔案之後，請使用下列 PowerShell 指令碼來啟動工作。 當工作完成時，輸出應該會寫入至 PowerShell 主控台。

####Hive

下列指令碼會執行 __streaming.py__ 指令碼。 執行前，會提示您輸入您 HDInsight 叢集的 HTTPs/系統管理帳戶資訊。

    # Replace 'YourHDIClusterName' with the name of your cluster
    $clusterName = YourHDIClusterName
    $creds=Get-Credential
    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        | %{ $_.Key1 }
    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey
            
    $HiveQuery = "add file wasb:///streaming.py;" +
                 "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
                   "USING 'D:\Python27\python.exe streaming.py' AS " +
                   "(clientid string, phoneLabel string, phoneHash string) " +
                 "FROM hivesampletable " +
                 "ORDER BY clientid LIMIT 50;"

    $jobDefinition = New-AzureRmHDInsightHiveJobDefinition `
        -Query $HiveQuery

    $job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds
    Write-Host "Wait for the Hive job to complete ..." -ForegroundColor Green
    Wait-AzureRmHDInsightJob `
        -JobId $job.JobId `
        -ClusterName $clusterName `
        -HttpCredential $creds
    # Uncomment the following to see stderr output
    # Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -DefaultContainer $container `
        -DefaultStorageAccountName $storageAccountName `
        -DefaultStorageAccountKey $storageAccountKey `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -DefaultContainer $container `
        -DefaultStorageAccountName $storageAccountName `
        -DefaultStorageAccountKey $storageAccountKey `
        -HttpCredential $creds

針對輸出 **Hive** 工作看起來應類似下列:

    100041  RIM 9650    d476f3687700442549a83fac4560c51c
    100041  RIM 9650    d476f3687700442549a83fac4560c51c
    100042  Apple iPhone 4.2.x  375ad9a0ddc4351536804f1d5d0ea9b9
    100042  Apple iPhone 4.2.x  375ad9a0ddc4351536804f1d5d0ea9b9
    100042  Apple iPhone 4.2.x  375ad9a0ddc4351536804f1d5d0ea9b9

####Pig

將使用下列 __jython.py__ 指令碼。 執行前，會提示您輸入 HDInsight 叢集的 HTTPs/系統管理資訊。

    # Replace 'YourHDIClusterName' with the name of your cluster
    $clusterName = YourHDIClusterName

    $creds = Get-Credential
    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        | %{ $_.Key1 }
    
    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey
            
    $PigQuery = "Register wasb:///jython.py using jython as myfuncs;" +
                "LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);" +
                "LOG = FILTER LOGS by LINE is not null;" +
                "DETAILS = foreach LOG generate myfuncs.create_structure(LINE);" +
                "DUMP DETAILS;"

    $jobDefinition = New-AzureRmHDInsightPigJobDefinition -Query $PigQuery

    $job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds
        
    Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
    Wait-AzureRmHDInsightJob `
        -Job $job.JobId `
        -ClusterName $clusterName `
        -HttpCredential $creds
    # Uncomment the following to see stderr output
    # Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -DefaultContainer $container `
        -DefaultStorageAccountName $storageAccountName `
        -DefaultStorageAccountKey $storageAccountKey `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -DefaultContainer $container `
        -DefaultStorageAccountName $storageAccountName `
        -DefaultStorageAccountKey $storageAccountKey `
        -HttpCredential $creds

針對輸出 **Pig** 工作看起來應類似下列:

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

##<a name="troubleshooting"></a>疑難排解

用來執行範例的兩個範例 PowerShell 指令碼都包含一行註解，此行會顯示工作的錯誤輸出。 如果您沒有看到預期的工作輸出，請將下列這一行取消註解，再查看錯誤資訊是否指出問題。

    # Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

錯誤資訊 (STDERR) 和工作結果 (STDOUT) 也會記錄到叢集的預設 Blob 容器 (在下列位置)。

關於此工作..|請在 Blob 容器中查看這些檔案
---|---
Hive|/HivePython/stderr<p>/HivePython/stdout
Pig|/PigPython/stderr<p>/PigPython/stdout

##<a name="next"></a>後續步驟

如果您需要載入非預設提供的 Python 模組，請參閱 [如何將模組部署至 Azure HDInsight](http://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx) 如需如何執行這項操作的範例。

若要了解使用 MapReduce，及Pig、Hive 的其他使用方式，請參閱下列內容。

* [搭配 HDInsight 使用 Hive](hdinsight-use-hive.md)

* [搭配 HDInsight 使用 Pig](hdinsight-use-pig.md)

* [將 HDInsight 與 MapReduce 搭配使用](hdinsight-use-mapreduce.md)

