<properties
    pageTitle="在 HDInsight 上將 Hadoop 工作的資料上傳 | Microsoft Azure"
    description="了解如何使用 Azure CLI、Azure 儲存體總管、Azure PowerShell、Hadoop 命令列或 Sqoop 在 HDInsight 中上傳及存取 Hadoop 工作。"
    services="hdinsight,storage"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/28/2015"
    ms.author="jgao"/>




# 在 HDInsight 上將 Hadoop 工作的資料上傳

Azure HDInsight 在 Azure Blob 儲存體上提供了全功能的 Hadoop 分散式檔案系統 (HDFS)。 此儲存體是設計為 HDFS 的延伸，以便為使用者提供流暢的體驗。 此儲存體可讓 Hadoop 生態系統中的完整元件集直接在它管理的資料上運作。 Azure Blob 儲存體和 HDFS 是不同的檔案系統，但經過最佳化後，都非常適合儲存資料以及計算儲存的資料。

**必要條件**

開始進行之前，請注意下列需求：

* Azure HDInsight 叢集。

## 為什麼要使用 Blob 儲存體？

部署 Azure HDInsight 叢集通常是為了執行 MapReduce 工作，並在這些工作完成後卸除叢集。 將計算完成後的資料保留在 HDFS 叢集是成本較高的資料儲存方式。 對於使用 HDInsight 來處理的資料，Azure Blob 儲存體是一種高可用性、高延展性、大容量、低成本且可共用的儲存方案。 將資料儲存在 Blob 中，可安全地釋放做為計算用途的 HDInsight 叢集，而且不會遺失資料。

### 目錄

Azure Blob 儲存體容器會以機碼/值組來儲存資料，而且沒有目錄階層。 然而，機碼名稱中可使用 "/" 字元，使檔案變成好像儲存在目錄結構中一樣。 HDInsight 會將它們視為就像是實際的目錄一樣。

例如，Blob 的機碼可能是 *input/log1.txt*。 實際上不存在 "input" 目錄，只是因為機碼名稱中有 "/" 字元，才形成檔案路徑的外觀。

因此，當您使用 Azure Explorer 工具時，可能會注意到一些 0 位元組的檔案。 這些檔案有兩種用途：

- 如果是空資料夾，這些檔案會標示資料夾的存在。 Azure Blob 儲存體很聰明，它知道如果有一個名為 foo/bar 的 Blob ，就有一個名為 **foo** 的資料夾。 但如果要表達有一個名為 **foo** 的空資料夾，唯一的辦法就是放入這個特殊的 0 位元組檔案。

- 這些檔案中保存 Hadoop 檔案系統所需的特殊中繼資料，尤其是資料夾的權限和擁有者。

## 命令列公用程式

Microsoft 提供下列公用程式來使用 Azure Blob 儲存體：

| 工具| Linux| OS X| Windows|
| ---- |:-----:|:----:|:-------:|
| | ✔| ✔| ✔|
| | | | ✔|
| | | | ✔|
| | ✔| ✔| ✔|

> [AZURE.NOTE] 雖然 Azure CLI、Azure PowerShell 與 AzCopy 都可從外部 Azure 使用，但是 Hadoop 命令只能在 HDInsight 叢集上使用，而且只允許將資料從本機檔案系統載入到 Azure Blob 儲存體。

### 

Azure CLI 是可讓您管理 Azure 服務的跨平台工具。 使用以下步驟將資料上傳至 Azure Blob 儲存體：

1. 

2. 開啟命令提示字元、Bash 或其他殼層，然後使用以下命令驗證您的 Azure 訂用帳戶。

        azure login

    出現提示時，請輸入訂用帳戶的使用者名稱和密碼。

3. 輸入以下命令可列出訂用帳戶的儲存體帳戶：

        azure storage account list

4. 選取含有您想要使用之 Blob 的儲存體帳戶，然後使用以下命令擷取此帳戶的金鑰：

        azure storage account keys list <storage-account-name>

    此時應該會傳回**主要**和**次要**金鑰。 複製**主要**金鑰值，因為接下來的步驟中會使用此值。

5. 使用以下命令擷取儲存體帳戶內的 Blob 儲存體清單：

        azure storage container list -a <storage-account-name> -k <primary-key>

6. 使用以下命令將檔案上傳及下載至 Blob：

    * 上傳檔案：

            azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>

    * 下載檔案：

            azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>


> [AZURE.NOTE] 如果您會持續使用同一個儲存體帳戶，可以設定以下環境變數，而不是為每個命令指定帳戶和金鑰：
>
> 
>
> 

### 

Azure PowerShell 是一種指令碼環境，可讓您在 Azure 中用來控制和自動化工作負載的部署及管理。

**將本機檔案上傳至 Azure Blob 儲存體**

1. 
2. 在下列指令碼中設定前五個變數的值：

     $subscriptionName = "<AzureSubscriptionName>"
     $resourceGroupName = "<AzureResourceGroupName>"
     $storageAccountName = "<StorageAccountName>"
     $containerName = "<ContainerName>"
    
     $fileName ="<LocalFileName>"
     $blobName = "<BlobName>"
    
     Switch-AzureMode -Name AzureResourceManager
    
     Add-AzureAccount
     Select-AzureSubscription $subscriptionName
    
     # Get the storage account key
     $storageaccountkey = get-azurestoragekey -ResourceGroupName $resourceGroupName -Name $storageAccountName | %{$_.Primary}
    
     # Create the storage context object
     $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
    
     # Copy the file from local workstation to the Blob container
     Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext

3. 將指令碼貼到 Azure PowerShell 主控台，並執行該指令碼來複製檔案。



### 

AzCopy 是一套命令列工具，此工具設計目的在於簡化將資料移入及移出 Azure 儲存體帳戶的傳輸工作。 您可以將它當做獨立工具來使用，也可以將此工具納入現有的應用程式中。

AzCopy 語法如下：

    AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]




### 

Hadoop 命令列僅適用於當資料已存在於叢集前端節點時，將資料儲存到 Blob 儲存體。

若要使用 Hadoop 命令，您必須先使用下列其中一個方法連線到前端節點：

* 

* 

連線之後，您就可以使用下列語法來將檔案上傳到儲存體。

    hadoop -copyFromLocal <localFilePath> <storageFilePath>



因為 HDInsight 的預設檔案系統是位於 Azure Blob 儲存體中，所以 /example/data.txt 實際上是在 Azure Blob 儲存體中。 您也可以用下列語法來參考此檔案：

    wasb:///example/data/data.txt

或

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt



## 圖形化用戶端

其他還有數個應用程式也會提供可搭配 Azure 儲存體使用的圖形化介面。 以下提供數個這類應用程式的清單：

| 用戶端| Linux| OS X| Windows|
| ------ |:-----:|:----:|:-------:|
| | ✔| ✔| ✔|
| | | | ✔|
| | | | ✔|
| | | | ✔|
| | ✔| ✔| ✔|
| | | ✔| ✔|

### 

*Azure 儲存體總管*是一種可在 Blob 中檢查和變更資料的實用工具。  原始碼亦可從此連結取得。

使用此工具之前，必須先知道您的 Azure 儲存體帳戶名稱和帳戶金鑰。

1. 執行 Azure 儲存體總管。 如果這是您第一次執行 [儲存體總管]，將會提示您輸入__儲存體帳戶名稱__和__儲存體帳戶金鑰__。 如果您之前曾執行過，請使用 [新增]____ 按鈕加入新的儲存體帳戶名稱和金鑰。

    輸入 HDinsight 叢集所使用儲存體帳戶的名稱和金鑰，然後選取 [儲存並開啟]____。

    ![HDI.AzureStorageExplorer][image-azure-storage-explorer]

5. 在此介面左邊的 [容器] 清單中，按一下與 HDInsight 叢集相關聯的容器名稱。 根據預設，這是 HDInsight 叢集的名稱，但如果您在建立叢集時輸入了特定名稱，則有可能不同。

6. 從工具列選取上傳圖示。

    ![工具列和反白顯示的上傳圖示](./media/hdinsight-upload-data/toolbar.png)

7. 指定要上傳的檔案，然後按一下 [開啟]****。 出現提示時，請選取 [上傳]____ 將檔案上傳至儲存體容器的根目錄。 如果您想要將檔案上傳到特定路徑，請在 [目的地]____ 欄位中輸入路徑，然後選取 [上傳]____。

    ![檔案上傳對話方塊](./media/hdinsight-upload-data/fileupload.png)

    完成上傳檔案之後，您可以從 HDInsight 叢集上的作業加以使用。

## 將 Azure Blob 儲存體掛接為本機磁碟機



## 服務

### Azure Data Factory

Azure Data Factory 服務是完全受管理的服務，可將資料儲存、資料處理及資料移動服務組合成有效率、可調整且可靠的資料生產管線。

Azure Data Factory 可用來將資料移至 Azure Blob 儲存體，或建立資料管線直接使用 HDInsight 功能，例如 Hive 和 Pig。



### 

Sqoop 是一種專門在 Hadoop 和關聯式資料庫之間傳送資料的工具。 此工具可讓您從 SQL Server、MySQL 或 Oracle 等關聯式資料庫管理系統 (RDBMS)，將資料匯入 Hadoop 分散式檔案系統 (HDFS)，使用 MapReduce 或 Hive 轉換 Hadoop 中的資料，然後將資料匯回 RDBMS。



## 開發 SDK

Azure Blob 儲存體也可以使用 Azure SDK，透過下列程式設計語言來存取：

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby




## 後續步驟

您現在已了解如何將資料匯入 HDInsight，請接著閱讀下列文章以了解如何執行分析：

* 
* 
* 
* [搭配使用 Pig 與 HDInsight ][hdinsight-use-pig]





[azure-management-portal]: https://porta.azure.com 
[azure-powershell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx 
[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/ 
[azure-create-storage-account]: ../storage-create-storage-account.md 
[azure-azcopy-download]: ../storage-use-azcopy.md 
[azure-azcopy]: ../storage-use-azcopy.md 
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md 
[hdinsight-storage]: ../hdinsight-use-blob-storage.md 
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md 
[hdinsight-get-started]: ../hdinsight-get-started.md 
[hdinsight-use-hive]: hdinsight-use-hive.md 
[hdinsight-use-pig]: hdinsight-use-pig.md 
[hdinsight-provision]: hdinsight-provision-clusters.md 
[sqldatabase-create-configure]: ../sql-database-create-configure.md 
[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html 
[powershell-install-configure]: ../powershell-install-configure.md 
[azurecli]: ../xplat-cli-install.md 
[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png 
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png 
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png 

