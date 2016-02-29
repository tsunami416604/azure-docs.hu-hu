<properties
   pageTitle="在 HDInsight 中搭配使用 Hadoop Hive 與 PowerShell | Microsoft Azure"
   description="使用 PowerShell 在 HDInsight 的 Hadoop 中執行 Hive 查詢"
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
   ms.date="11/02/2015"
   ms.author="larryfr"/>

#使用 PowerShell 執行 Hive 查詢

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

本文件提供在 Azure 資源群組模式中使用 Azure PowerShell 的範例，以便在 HDInsight 叢集的 Hadoop 中執行 Hive 查詢。 使用 Azure PowerShell 在 Azure 服務模式下，請參閱 [執行 Hive 查詢使用 PowerShell ASM 模式](hdinsight-hadoop-use-hive-powershell-v1.md)。

> [AZURE.NOTE] 這份文件不提供範例中所用的 HiveQL 陳述式所執行的工作詳細的的描述。 如需此範例中使用的 HiveQL 的相關資訊，請參閱[搭配使用 Hive 與 HDInsight 上的 Hadoop](hdinsight-use-hive.md)。


**必要條件**

若要完成這篇文章中的步驟，您需要下列項目。

- **Azure HDInsight (HDInsight 上的 Hadoop) 叢集 (Windows 型或 Linux 型)**
- **具有 Azure PowerShell 的工作站**。 請參閱 [安裝 Azure PowerShell 1.0 和更新版本所](hdinsight-administer-use-powershell.md#install-azure-powershell-10-and-greater)。

##使用 Azure PowerShell 執行 Hive 查詢

Azure PowerShell 提供 *cmdlet* 可讓您從遠端在 HDInsight 上執行 Hive 查詢。 在內部，您可以使用在 HDInsight 叢集上執行的 [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (先前稱為 Templeton) 的 REST 呼叫來達到此目的。

在遠端 HDInsight 叢集中執行 Hive 查詢時，會使用下列 Cmdlet：

* **新增 AzureRmAccount**: 驗證 PowerShell 至您的 Azure 訂閱

* **新 AzureRmHDInsightHiveJobDefinition**: 建立新 *作業定義* 使用指定的 HiveQL 陳述式

* **開始 AzureRmHDInsightJob**: 將工作定義傳送至 HDInsight、 啟動工作，並傳回 *工作* 物件，可用來檢查工作狀態

* **等候 AzureRmHDInsightJob**: 使用工作物件來檢查工作狀態。 它會等到工作完成，或等到等候時間超過。

* **Get AzureRmHDInsightJobOutput**: 用來擷取工作的輸出

* **叫用 AzureRmHDInsightHiveJob**: 用來執行 HiveQL 陳述式。 這會阻止查詢完成，然後傳回結果

* **使用 AzureRmHDInsightCluster**: 設定要用於目前的叢集 **Invoke AzureRmHDInsightHiveJob** 命令

下列步驟示範如何使用這些 Cmdlet，在您的 HDInsight 叢集中執行工作：

1. 使用編輯器，將儲存為下列程式碼 **hivejob.ps1**。 您必須將 **CLUSTERNAME** 與 HDInsight 叢集的名稱。

        #Specify the values
        $clusterName = "CLUSTERNAME"
        $creds=Get-Credential
                
        # Login to your Azure subscription
        # Is there an active Azure subscription?
        $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Add-AzureRmAccount
        }

        #HiveQL
        $queryString = "DROP TABLE log4jLogs;" +
                       "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb:///example/data/';" +
                       "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"

        #Create an HDInsight Hive job definition
        $hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString 

        #Submit the job to the cluster
        Write-Host "Start the Hive job..." -ForegroundColor Green

        $hiveJob = Start-AzureRmHDInsightJob -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $creds


        #Wait for the Hive job to complete
        Write-Host "Wait for the job to complete..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $creds

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup `
            | %{ $_.Key1 }
        # Print the output
        Write-Host "Display the standard output..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $hiveJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds
            
2. 開啟新 **PowerShell** 命令提示字元。 將目錄變更至位置 **hivejob.ps1** 檔案，然後使用下列命令來執行指令碼:

        .\hivejob.ps1

    執行指令碼時，系統會提示輸入您叢集的 HTTPS/系統管理帳戶認證。 可能也會提示登入您的 Azure 訂用帳戶。
    
7. 工作完成時，應該會傳回與下面類似的資訊：

        Display the standard output...
        [ERROR] 3

4. 如前所述， **Invoke-hive** 可用來執行查詢，並等候回應。 使用下列命令，並將 **CLUSTERNAME** 與您的叢集名稱:

        Use-AzureRmHDInsightCluster -ClusterName $clusterName
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup `
            | %{ $_.Key1 }
        Invoke-AzureRmHDInsightHiveJob `
            -StatusFolder "wasb:///example/statusout" `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -Query @"
        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
        SELECT * FROM errorLogs;
        "@

    輸出顯示如下：

        2012-02-03  18:35:34    SampleClass0    [ERROR] incorrect   id
        2012-02-03  18:55:54    SampleClass1    [ERROR] incorrect   id
        2012-02-03  19:25:27    SampleClass4    [ERROR] incorrect   id

    > [AZURE.NOTE] 較長的 HiveQL 查詢，您可以使用 Azure PowerShell **Here-strings** cmdlet 或 HiveQL 指令碼檔案。 下列程式碼片段示範如何使用 **Invoke-hive** cmdlet 來執行 HiveQL 指令碼檔案。 必須將 HiveQL 指令碼檔案上傳至 wasb://。
    >
    > `Invoke-AzureRmHDInsightHiveJob -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
    >
    > 如需詳細資訊 **Here-strings**, ，請參閱 <a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">使用 Windows PowerShell Here-strings</a>。

##疑難排解

如果在工作完成時未傳回任何資訊，則可能是處理期間發生錯誤。 若要檢視這項工作的錯誤資訊，結尾加入下列 **hivejob.ps1** 檔案，並儲存它，然後再次執行。

    # Print the output of the Hive job.
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

這會傳回執行工作時寫入至伺服器上之 STDERR 的資訊，而且可能有助於判斷工作的失敗原因。

##摘要

如您所見，Azure PowerShell 提供簡單的方法，在 HDInsight 叢集中執行 Hive 查詢、監視工作狀態，以及擷取輸出。

##後續步驟

如需 HDInsight 中 Hive 的一般資訊：

* [搭配使用 Hive 與 HDInsight 上的 Hadoop](hdinsight-use-hive.md)

如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊：

* [搭配使用 Pig 與 HDInsight 上的 Hadoop](hdinsight-use-pig.md)

* [搭配使用 MapReduce 與 HDInsight 上的 Hadoop](hdinsight-use-mapreduce.md)

