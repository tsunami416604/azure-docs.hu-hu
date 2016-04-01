<properties
    pageTitle="在 Azure 資料處理站管線中使用自訂活動"
    description="了解如何建立自訂活動，並在 Azure 資料處理站管線中使用這些活動。"
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
    ms.date="12/15/2015"
    ms.author="spelluru"/>

# 在 Azure 資料處理站管線中使用自訂活動
Azure Data Factory 支援內建活動，例如 **複製活動** 和 **HDInsight 活動** ，用於在管線中進行移動和處理資料。 您也可以使用您自己的轉換/處理邏輯建立自訂的 .Net 活動，並在管線中使用該活動。 您可以設定要執行使用的活動 **Azure HDInsight** 叢集或 **Azure 批次** 服務。   

本文說明如何建立自訂活動，並在 Azure 資料處理站管線中使用它。 本文也提供建立及使用自訂活動的詳細逐步解說與逐步指示。 本逐步解說會使用 HDInsight 連結服務。 若要改用 Azure Batch 連結服務，您建立連結的服務型別的 **AzureBatch** 並將它用於管線 JSON 的活動區段 (**linkedServiceName**)。 請參閱 [Azure Batch 連結服務](#AzureBatch) 區段，如需有關使用 Azure Batch 搭配自訂活動。


## <a name="walkthrough" /> 逐步解說：
本逐步解說為您提供建立自訂活動以及在 Azure Data Factory 管線中使用該活動的逐步指示。 本逐步解說延伸的教學課程 [開始使用 Azure Data Factory][adfgetstarted]。 如果您想要看自訂活動運作，您需要先依照入門教學課程操作，然後執行此逐步解說。

### 必要條件


- 教學課程 [開始使用 Azure Data Factory][adfgetstarted]。 您必須完成本文中的教學課程，再執行本逐步解說。
- Visual Studio 2012 或 2013
- 下載並安裝 [Azure.NET SDK][azure-developer-center]
- 下載最新 [Azure Data Factory 的 NuGet 封裝](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories/) 並安裝它。 逐步解說中包含相關指示。
- 下載並安裝 Azure 儲存體的 NuGet 封裝。 逐步解說中有指示，因此您可以略過此步驟。

### 高階步驟 
1.  **建立自訂活動** 在 Data Factory 方案中使用。 自訂活動包含資料處理邏輯。 
    1.  在 Visual Studio (或選擇的程式碼編輯器) 中，建立 .NET 類別庫專案，加入程式碼以處理輸入的資料，並編譯專案。  
    2.  壓縮輸出資料夾中所有的二進位檔和 PDB (選擇性) 檔案。  
    3.  將 zip 檔案上傳至 Azure Blob 儲存體。 在建立自訂活動一節中有詳細的步驟。 
2. **建立使用自訂活動的 Azure data factory**:
    1. 建立 Azure Data Factory。
    2. 建立連結的服務。
        1. StorageLinkedService：提供用於存取 blob 的儲存體認證。
        2. HDInsightLinkedService：指定做為計算的 Azure HDInsight。
    3. 建立資料集。
        1. InputDataset：指定輸入 blob 的儲存體容器和資料夾。
        1. OuputDataset：指定輸出 blob 的儲存體容器和資料夾。
    2. 建立使用自訂活動的管線。
    3. 執行與測試管線。
    4. 偵錯管線。

## 建立自訂活動
若要建立的.NET 自訂活動，您可以使用 Azure Data Factory 管線中，您必須建立 **.NET 類別庫** 專案的類別實作， **IDotNetActivity** 介面。 這個介面只有一個方法：執行。 以下是該方法的簽章：

    public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices, 
            IEnumerable<Dataset> datasets, 
            Activity activity, 
            IActivityLogger logger)
        
此方法有幾個您必須了解的關鍵元件。 

- 此方法會採用四個參數：
    - **linkedServices**。 這是將輸入/輸出資料來源 (例如：Azure Blob 儲存體) 連結到 Data Factory 的連結服務列舉清單。 在此範例中，只有一個用於輸入和輸出的 Azure 儲存體類型連結服務。 
    - **資料集**。 這是資料集的可列舉清單。 您可以使用這個參數取得輸入和輸出資料集定義的位置和結構描述。
    - **活動**。 這個參數代表目前的計算實體 - 在此情況下為 Azure HDInsight。
    - **記錄器**。 記錄器可讓您撰寫會呈現為管線的「使用者」記錄檔的偵錯註解。 

- 此方法會傳回可用來將自訂活動鏈結在一起的字典。 我們不會在此範例解決方案中使用這項功能。 

### 程序： 
1.  建立 .NET 類別庫專案。
    <ol type="a">
        <li>啟動 <b>Visual Studio 2012</b> 或 <b>Visual Studio 2013</b>。</li>
        <li>按一下 [ <b>檔案</b>, ，指向 [ <b>新增</b>, ，然後按一下 <b>專案</b>。</li>
        <li>展開 <b>範本</b>, ，然後選取 <b>Visual C#</b>。 在此逐步解說中，您使用 C# 中，但您可以使用任何 .NET 語言來開發自訂活動。</li>
        <li>選取 <b>類別庫</b> 從右邊的專案類型清單。</li>
        <li>輸入 <b>MyDotNetActivity</b> 的 <b>名稱</b>。</li>
        <li>選取 <b>C:\ADFGetStarted</b> 的 <b>位置</b>。</li>
        <li>按一下 [ <b>確定</b> 以建立專案。</li>
    </ol>
2.  按一下 [<b>工具</b>]，指向 [<b>NuGet 封裝管理員</b>]，然後按一下 [<b>封裝管理員主控台</b>]。
3.  在 [封裝管理員主控台]<b></b> 中，執行下列命令匯入 <b>Microsoft.Azure.Management.DataFactories</b>。

        Install-Package Microsoft.Azure.Management.DataFactories

4. 將 Azure 儲存體 NuGet 封裝匯入專案。

        Install-Package Azure.Storage

5. 新增下列 **使用** 原始程式檔在專案中的陳述式。

        using System.IO;
        using System.Globalization;
        using System.Diagnostics;
        using System.Linq;

        using Microsoft.Azure.Management.DataFactories.Models;
        using Microsoft.Azure.Management.DataFactories.Runtime;

        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

6. 變更名稱 **命名空間** 至 **MyDotNetActivityNS**。

        namespace MyDotNetActivityNS

7. 變更類別名稱 **MyDotNetActivity** 和從它衍生出來 **IDotNetActivity** 介面，如下所示。

        public class MyDotNetActivity : IDotNetActivity

8. 實作 （加入） **Execute** 方法 **IDotNetActivity** 介面 **MyDotNetActivity** 類別，並將下列範例程式碼複製到方法。

    下列範例程式碼會計算輸入 Blob 中的行數，並在輸出 Blob 中產生下列內容：Blob 的路徑、Blob 中的行數、執行活動的電腦、目前的日期時間。

        /// <summary>
        /// Execute method is the only method of IDotNetActivity interface you must implement. 
        /// In this sample, the method invokes the Calculate method to perform the core logic.  
        /// </summary>

        public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
        {

            // declare types for input and output data stores
            AzureStorageLinkedService inputLinkedService;

            // declare dataset types
            CustomDataset inputLocation;
            AzureBlobDataset outputLocation;

            Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
            inputLocation = inputDataset.Properties.TypeProperties as CustomDataset;

            foreach (LinkedService ls in linkedServices)
                logger.Write("linkedService.Name {0}", ls.Name);

            // using First method instead of Single since we are using the same 
            // Azure Storage linked service for input and output. 
            inputLinkedService = linkedServices.First(
                linkedService =>
                linkedService.Name ==
                inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
                as AzureStorageLinkedService;

            string connectionString = inputLinkedService.ConnectionString; // To create an input storage client.
            string folderPath = GetFolderPath(inputDataset);
            string output = string.Empty; // for use later.

            // create storage client for input. Pass the connection string.
            CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
            CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

            // initialize the continuation token before using it in the do-while loop.
            BlobContinuationToken continuationToken = null;
            do
            {   // get the list of input blobs from the input storage client object.
                BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
                                         true,
                                         BlobListingDetails.Metadata,
                                         null,
                                         continuationToken,
                                         null,
                                         null);
                
                // Calculate method returns the number of occurrences of 
                // the search term (“Microsoft”) in each blob associated
                // with the data slice. 
                // 
                // definition of the method is shown in the next step.
 
                output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

            } while (continuationToken != null);

            // get the output dataset using the name of the dataset matched to a name in the Activity output collection.
            Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
            // convert to blob location object.
            outputLocation = outputDataset.Properties.TypeProperties as AzureBlobDataset;

            folderPath = GetFolderPath(outputDataset);

            logger.Write("Writing blob to the folder: {0}", folderPath);

            // create a storage object for the output blob.
            CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
            // write the name of the file. 
            Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

            logger.Write("output blob URI: {0}", outputBlobUri.ToString());
            // create a new blob and upload the output text.
            CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
            logger.Write("Writing {0} to the output blob", output);
            outputBlob.UploadText(output);

            // return a new Dictionary object (unused in this code).
            return new Dictionary<string, string>();
        }

9. 新增下列 Helper 方法。  **Execute** 方法會叫用這些 helper 方法。  **GetConnectionString** 方法會擷取 Azure 儲存體連接字串和 **GetFolderPath** 方法會擷取 blob 位置。 最重要的是， **Calculate** 方法隔離程式碼會逐一查看每個 blob。

        /// <summary>
        /// Gets the folderPath value from the input/output dataset.   
        /// </summary>

        private static string GetFolderPath(Dataset dataArtifact)
        {
            if (dataArtifact == null || dataArtifact.Properties == null)
            {
                return null;
            }

            AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
            if (blobDataset == null)
            {
                return null;
            }

            return blobDataset.FolderPath;
        }

        /// <summary>
        /// Gets the fileName value from the input/output dataset.   
        /// </summary>

        private static string GetFileName(Dataset dataArtifact)
        {
            if (dataArtifact == null || dataArtifact.Properties == null)
            {
                return null;
            }

            AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
            if (blobDataset == null)
            {
                return null;
            }

            return blobDataset.FileName;
        }

        /// <summary>
        /// Iterates through each blob (file) in the folder, counts the number of instances of search term in the file, 
        /// and prepares the output text that will be written to the output blob. 
        /// </summary>

        public static string Calculate(BlobResultSegment Bresult, IActivityLogger logger, string folderPath, ref BlobContinuationToken token, string searchTerm)
        {
            string output = string.Empty;
            logger.Write("number of blobs found: {0}", Bresult.Results.Count<IListBlobItem>());
            foreach (IListBlobItem listBlobItem in Bresult.Results)
            {
                CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
                if ((inputBlob != null) && (inputBlob.Name.IndexOf("$$$.$$$") == -1))
                {
                    string blobText = inputBlob.DownloadText(Encoding.ASCII, null, null, null);
                    logger.Write("input blob text: {0}", blobText);
                    string[] source = blobText.Split(new char[] { '.', '?', '!', ' ', ';', ':', ',' }, StringSplitOptions.RemoveEmptyEntries);
                    var matchQuery = from word in source
                                     where word.ToLowerInvariant() == searchTerm.ToLowerInvariant()
                                     select word;
                    int wordCount = matchQuery.Count();
                    output += string.Format("{0} occurrences(s) of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
                }
            }
            return output;
        }

    GetFolderPath 方法會將路徑傳回資料集所指向的資料夾，而 GetFileName 方法會傳回資料集指向的 blob/檔案名稱。 
    
            "name": "InputDataset",
            "properties": {
                "type": "AzureBlob",
                "linkedServiceName": "StorageLinkedService",
                "typeProperties": {
                    "fileName": "file.txt",
                    "folderPath": "mycontainer/inputfolder/",
    
    Calculate 方法會在輸入檔案 (資料夾中的 blob) 計算關鍵字 Microsoft 的執行個體數目。 搜尋詞彙 ("Microsoft") 已在程式碼中硬式編碼。

10. 編譯專案。 按一下 [ **建置** 從功能表，然後按一下 **建置方案**。
11. 啟動 **Windows 檔案總管**, ，並瀏覽至 **bin\debug** 或 **bin\release** 資料夾建置類型而定。
12. 建立 zip 檔案 **MyDotNetActivity.zip** ，其中包含中的所有二進位檔 <project folder>\bin\Debug 資料夾中。 您可能想要包含 **MyDotNetActivity.pdb** 檔案，所以您在故障的問題原因的原始程式碼中取得其他詳細資料，例如行號。

    ![二進位輸出檔案](./media/data-factory-use-custom-activities/Binaries.png)
13. 上傳 **MyDotNetActivity.zip** 為 blob 容器的 blob: **customactvitycontainer** 在 Azure blob 儲存體， **StorageLinkedService** 連結服務中的 **ADFTutorialDataFactory** 使用。  建立 blob 容器 **customactivitycontainer** 如果不存在。

> [AZURE.NOTE] 如果您將此.NET 活動專案加入方案包含 Data Factory 專案的 Visual Studio 中，您不需要執行兩個步驟建立的 zip 檔案，並以手動方式將資料上傳至 Azure blob 儲存體。 當您使用 Visual Studio 發佈 Data Factory 實體時，發佈程序會自動完成這些步驟。 請參閱 [建置您使用 Visual Studio 的第一個管線](data-factory-build-your-first-pipeline-using-vs.md) 和 [從 Azure Blob 複製資料到 Azure SQL](data-factory-get-started-using-vs.md) 文件以了解建立和發行使用 Visual Studio 的 Data Factory 實體。  

### 執行方法

本節提供詳細資訊，並在程式碼的相關注意事項 **Execute** 方法。
 
1. 逐一查看輸入集合的成員可在 [Microsoft.WindowsAzure.Storage.Blob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.aspx) 命名空間。 逐一查看 blob 集合時需要使用 **BlobContinuationToken** 類別。 基本上，您必須搭配使用 do-while 迴圈和權杖做為結束迴圈的機制。 如需詳細資訊，請參閱 [如何使用 Blob 儲存體.NET](../storage/storage-dotnet-how-to-use-blobs.md)。 基本迴圈如下所示：

        // Initialize the continuation token.
        BlobContinuationToken continuationToken = null;
        do
        {   
            // Get the list of input blobs from the input storage client object.
            BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
                                      true,
                                      BlobListingDetails.Metadata,
                                      null,
                                      continuationToken,
                                      null,
                                      null);
            // Return a string derived from parsing each blob.
            output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
        } while (continuationToken != null);

    請參閱文件 [ListBlobsSegmented](https://msdn.microsoft.com/library/jj717596.aspx) 方法，如需詳細資訊。

2.  以邏輯方式逐一查看 blob 集的程式碼會在 do-while 迴圈中執行。 在 **Execute** 方法中，執行-雖然迴圈傳遞至方法，名為的 blob 清單 **Calculate**。 方法會傳回名為的字串變數 **輸出** 也就是需要逐一區段中的所有 blob 的結果。 

    它會傳回搜尋詞彙的發生次數 (**Microsoft**) 傳遞至 blob 中 **Calculate** 方法。 

            output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);

3.  一次 **Calculate** 方法完成的工作，則必須寫入至新的 blob。 因此對於每個處理過的 blob 集，都可以利用結果撰寫新的 blob。 若要寫入新的 blob，請先找到輸出資料集。 

            // Get the output dataset using the name of the dataset matched to a name in the Activity output collection.
            Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

            // Convert to blob location object.
            outputLocation = outputDataset.Properties.TypeProperties as AzureBlobDataset;

4.  程式碼也會呼叫 helper 方法 ︰ **GetFolderPath** 擷取資料夾路徑 （儲存體容器名稱）。
 
            folderPath = GetFolderPath(outputDataset);

     **GetFolderPath** AzureBlobDataSet，有一個叫做 FolderPath 屬性的資料集物件會轉換。
            
            AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
            
            return blobDataset.FolderPath;

5.  程式碼會呼叫 **GetFileName** 方法來擷取檔案名稱 （blob）。 程式碼取得資料夾路徑的方式類似上述程式碼。 

            AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

            return blobDataset.FileName;

6.  藉由建立新的 URI 物件寫入檔案的名稱。 URI 的建構函式使用 **BlobEndpoint** 屬性傳回的容器名稱。 新增資料夾路徑和檔案名稱以建構輸出 blob URI。  

            // Write the name of the file. 
            Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

7.  已寫入檔案名稱，現在您可以從 Calculate 方法將輸出字串寫入新的 blob：

            // Create a new blob and upload the output text.
            CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
            logger.Write("Writing {0} to the output blob", output);
            outputBlob.UploadText(output);

## 建立 Data Factory

在 **建立自訂活動** ] 區段中，您建立自訂活動，並上傳至 Azure blob 容器的 zip 檔案與二進位檔和 PDB 檔案。 在本節中，您將建立 Azure **資料 factory** 與 **管線** 使用 **自訂活動**。
 
自訂活動的輸入資料集代表 blob 儲存體中輸入資料夾 (mycontainer\inputfolder) 的 blob (檔案)。 活動的輸出資料集代表 blob 儲存體中輸出資料夾 (mycontainer\outputfolder) 的輸出 blob。 

利用下列內容建立名為 file.txt 的檔案並將它上傳至 mycontainer\inputfolder (mycontainer 是 Azure blob 容器的名稱而 inputfolder 是該容器中的資料夾名稱)。

    test custom activity Microsoft test custom activity Microsoft

即使資料夾有 2 個以上的檔案，輸入資料夾還是會對應至 Azure Data Factory 中的配量。 管線處理每個配量時，自訂活動會為該配量逐一查看輸入資料夾中的所有 blob。 

您會看到 mycontainer\output 資料夾中的一個輸出檔案具有 1 行或更多行 (和輸入資料夾中的 blob 數目相同)：
 
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.


以下是您將在此節中執行的步驟：

1. 建立 **資料 factory**。
2. 建立 **連結服務** HDInsight 叢集為僅限對應的工作和 Azure 儲存體所在的輸入/輸出 blob 將執行的自訂活動。 
2. 建立輸入和輸出 **資料集** 代表輸入和輸出的自訂活動。 
3. 建立和執行 **管線** ，使用自訂活動。

### 步驟 1：建立 Data Factory

1.  登入 Azure 入口網站之後，執行下列動作：
    1.  按一下 [ **新增** 左側功能表。
    2.  按一下 [ **資料 + 分析** 中 **新增** 刀鋒視窗。
    3.  按一下 [ **Data Factory** 上 **資料分析** 刀鋒視窗。
2.  在 **新增 data factory** 刀鋒視窗中，輸入 **CustomActivityFactory** 的名稱。 Azure Data Factory 的名稱在全域必須是唯一的。 如果您收到錯誤 ︰ **Data factory 名稱"CustomActivityFactory"沒有**, ，變更 data factory 的名稱 (例如， **yournameCustomActivityFactory**)，然後再試一次建立。
3.  按一下 [ **資源群組名稱**, ，然後選取現有的資源群組或建立新的資源群組。 
4.  確認您使用正確 **訂閱** 和 **區域** 想要建立的資料處理站。 
5.  按一下 [ **建立** 上 **新增 data factory** 刀鋒視窗。
6.  您會看到 data factory 中建立 **儀表板** 的 Azure 入口網站。
7.  在 Data Factory 成功建立後，您會看到 Data Factory 刀鋒視窗，顯示 Data Factory 的內容。

### 步驟 2：建立連結服務

連結服務會將資料存放區或計算服務連結至 Azure Data Factory。 在此步驟中，您將您的 Azure 儲存體帳戶和 Azure HDInsight 叢集連結到您的 Data Factory。

#### 建立 Azure 儲存體連結服務

1.  按一下 [ **作者和部署** 磚 **DATA FACTORY** 分頁 **CustomActivityFactory**。 這會啟動 Data Factory 編輯器。
2.  按一下 [ **新增資料存放區** 命令列，然後選擇 [ **Azure 儲存體**。 在編輯器中，您應該會看到用來建立 Azure 儲存體連結服務的 JSON 指令碼。
3.  取代 **帳戶名稱** 的 Azure 儲存體帳戶名稱和 **帳戶金鑰** 與 Azure 儲存體帳戶的存取金鑰。 若要了解如何取得儲存體存取金鑰，請參閱 [檢視、 複製和重新產生儲存體存取金鑰](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)。
4.  按一下 [ **部署** 命令列來部署連結的服務。


#### 建立 Azure HDInsight 連結服務 
Azure Data Factory 服務支援建立隨選叢集，並使用它處理輸入來產生輸出資料。 您也可以使用自己的叢集執行相同作業。 當您使用隨選 HDInsight 叢集時，系統會為每個配量建立叢集。 然而，如果您使用自己的 HDInsight 叢集，叢集已經準備好立即處理配量。 因此，在使用隨選叢集時，可能無法像使用自己的叢集那麼快看到輸出資料。

> [AZURE.NOTE] 在執行階段，.NET 活動的執行個體只會在執行中 HDInsight 叢集，一個背景工作節點無法擴展到多個節點上執行。 .NET 活動的多個執行個體可以在 HDInsight 叢集的不同節點上平行執行。

如果您已延伸 [開始使用 Azure Data Factory][adfgetstarted] 教學課程的逐步解說使用 [搭配 Azure Data Factory 使用 Pig 和 Hive][hivewalkthrough], ，您可以略過建立此連結服務，並使用 ADFTutorialDataFactory 中已有的連結的服務。


##### 若要使用隨選 HDInsight 叢集

1. 在 **Azure 入口網站**, ，按一下 [ **製作和部署** Data Factory 首頁中。
2. 在 Data Factory 編輯器中，按一下 [ **新增計算** 從命令列，然後選取 **隨 HDInsight 叢集** 從功能表。
2. 在 JSON 指令碼中，執行下列動作：
    1. 如 **clusterSize** 屬性，指定 HDInsight 叢集的大小。
    3. 如 **timeToLive** 屬性，指定時間長度會在刪除之前客戶可處於閒置狀態。
    4. 如 **版本** 屬性，指定您想要使用的 HDInsight 版本。 如果您排除此屬性，則會使用最新版本。  
    5. 如 **linkedServiceName**, ，指定 **StorageLinkedService** 您已建立在 「 開始使用 」 教學課程。

            {
              "name": "HDInsightOnDemandLinkedService",
              "properties": {
                "type": "HDInsightOnDemand",
                "typeProperties": {
                  "clusterSize": "1",
                  "timeToLive": "00:05:00",
                  "version": "3.2",
                  "linkedServiceName": "StorageLinkedService"
                }
              }
            }

2. 按一下 [ **部署** 命令列來部署連結的服務。

##### 若要使用您自己的 HDInsight 叢集：

1. 在 **Azure 入口網站**, ，按一下 [ **製作和部署** Data Factory 首頁中。
2. 在 **Data Factory 編輯器**, ，按一下 [ **新增計算** 從命令列，然後選取 **hdinsight** 從功能表。
2. 在 JSON 指令碼中，執行下列動作：
    1. 如 **clusterUri** 屬性中，輸入您的 HDInsight 的 URL。 例如 ︰ https://<clustername>.azurehdinsight.net/     
    2. 如 **UserName** 屬性中，輸入具有 HDInsight 叢集存取權的使用者名稱。
    3. 如 **密碼** 屬性中，輸入使用者的密碼。
    4. 如 **LinkedServiceName** 屬性中，輸入 **StorageLinkedService**。 這是您在「開始使用」教學課程中建立的連結服務。

2. 按一下 [ **部署** 命令列來部署連結的服務。

### 步驟 3：建立資料集
在此步驟中，您將建立資料集來代表輸入和輸出資料。

#### 建立輸入資料集
1.  在 **編輯器** Data Factory 中，按一下 [ **新的資料集** 按鈕的工具列，並按一下 **Azure Blob 儲存體** 從下拉式功能表。
2.  使用下列 JSON 程式碼片段取代右窗格中的 JSON：

            {
                "name": "InputDataset",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "StorageLinkedService",
                    "typeProperties": {
                        "folderPath": "adftutorial/customactivityinput/",
                        "format": {
                            "type": "TextFormat"
                        }
                    },
                    "availability": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "external": true,
                    "policy": {}
                }
            }

    您稍後將在本逐步解說建立管線，開始時間為：2015-11-16T00:00:00Z，而結束時間為：2015-11-16T05:00:00Z。 排程每小時、 產生的資料應該會有 5 個輸入/輸出配量 (之間 **00**: 00:00]-> [ **05**: 00:00)。 

     **頻率** 和 **間隔** 輸入資料集設定為 **小時** 和 **1**, ，這表示已輸入配量可供每小時。 在此範例中，它是 intputfolder 中的相同檔案 (file.txt)。 

    以下是每個配量的開始時間，由上述 JSON 程式碼片段中的 SliceStart 系統變數代表。 

    
3.  按一下 [ **部署** 建立和部署] 工具列上 **InputDataset**。 確認您看到 **已成功建立資料表** 編輯器的標題列上的訊息。


#### 建立輸出資料表

1. 在 **Data Factory 編輯器**, ，按一下 [ **新的資料集**, ，然後按一下 [ **Azure Blob 儲存體** 從命令列。
2. 使用下列 JSON 指令碼取代右窗格中的 JSON 指令碼：

        {
            "name": "OutputDataset",
            "properties": {
                "type": "AzureBlob",
                "linkedServiceName": "StorageLinkedService",
                "typeProperties": {
                    "fileName": "{slice}.txt",
                    "folderPath": "adftutorial/customactivityoutput",
                    "partitionedBy": [
                        {
                            "name": "slice",
                            "value": {
                                "type": "DateTime",
                                "date": "SliceStart",
                                "format": "yyyy-MM-dd-HH"
                            }
                        }
                    ]
                },
                "availability": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
        }

    輸出位置是 **adftutorial/customactivityoutput/YYYYMMDDHH/** 其中 YYYYMMDDHH 是年、 月、 日和小時產生配量。 請參閱 [開發人員參考][adf-developer-reference] 如需詳細資訊。

    為每個輸入配量產生輸出 blob/檔案。 以下是為每個配量命名輸出檔案的方式。 所有的輸出檔會產生一個輸出資料夾中 ︰ **adftutorial\customactivityoutput**。

  	| 配量 | 開始時間 | 輸出檔案 |
  	| :---- | :--------- | :---------- | 
  	| 1 | 2015-11-16T00:00:00 | 2015-11-16-00.txt |
  	| 2 | 2015-11-16T01:00:00 | 2015-11-16-01.txt |
  	| 3 | 2015-11-16T02:00:00 | 2015-11-16-02.txt |
  	| 4 | 2015-11-16T03:00:00 | 2015-11-16-03.txt |
  	| 5 | 2015-11-16T04:00:00 | 2015-11-16-04.txt |

    請記得輸入資料夾中的所有檔案都是包含上述開始時間之配量的一部分。 處理此配量時，自訂活動會掃描每個檔案，並利用搜尋詞彙 (“Microsoft”) 的出現次數在輸出檔案中產生資料行。 如果 inputfolder 中有三個檔案，每小時的配量會有三行在輸出檔案中：2015-11-16-00.txt、2015-11-16:01:00:00.txt等等...。 


2. 按一下 [ **部署** 命令列來部署 **OutputDataset**。


### 建立並執行使用自訂活動的管線

1. 在 Data Factory 編輯器中，按一下 [ **新增管線** 命令列上。 如果看不到此命令，請按一下 [...]**** (省略符號) 就可看到。
2. 使用下列 JSON 指令碼取代右窗格中的 JSON。 如果您想要使用您自己的叢集，並遵循步驟建立 **HDInsightLinkedService** 連結服務，取代 **HDInsightOnDemandLinkedService** 與 **HDInsightLinkedService** 下列 JSON 中。

        {
          "name": "ADFTutorialPipelineCustom",
          "properties": {
            "description": "Use custom activity",
            "activities": [
              {
                "Name": "MyDotNetActivity",
                "Type": "DotNetActivity",
                "Inputs": [
                  {
                    "Name": "InputDataset"
                  }
                ],
                "Outputs": [
                  {
                    "Name": "OutputDataset"
                  }
                ],
                "LinkedServiceName": "HDInsightOnDemandLinkedService",
                "typeProperties": {
                  "AssemblyName": "MyDotNetActivity.dll",
                  "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
                  "PackageLinkedService": "StorageLinkedService",
                  "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
                  "extendedProperties": {
                    "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
                  }
                },
                "Policy": {
                  "Concurrency": 1,
                  "ExecutionPriorityOrder": "OldestFirst",
                  "Retry": 3,
                  "Timeout": "00:30:00",
                  "Delay": "00:00:00"
                }
              }
            ],
            "start": "2015-11-16T00:00:00Z",
            "end": "2015-11-16T05:00:00Z",
            "isPaused": false
          }
        }

    取代 **StartDateTime** 目前日期的前三天的值和 **EndDateTime** 為目前日期的值。 StartDateTime 和 enddatetime 都必須位於 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。 例如：2014-10-14T16:32:41Z。 將輸出資料表排定為每天產生，因此將產生三個配量。

    請注意：

    - 在活動區段中會有一個活動，它的型別 ︰ **DotNetActivity**。
    - 使用相同的輸入的資料表 **EmpTableFromBlob** 用於 「 開始使用 」 教學課程。
    - 使用新的輸出資料表 **OutputTableForCustom** 您將在下一個步驟中建立。
    - **AssemblyName** 設定為 DLL 的名稱 ︰ **MyActivities.dll**。
    - **進入點** 設為 **MyDotNetActivityNS.MyDotNetActivity**。
    - **PackageLinkedService** 設為 **StorageLinkedService** ，它會指向包含自訂活動 zip 檔案的 blob 儲存體。 如果您將不同的 Azure 儲存體帳戶用於輸入/輸出檔案和自訂活動 zip 檔案，您必須建立另一個 Azure 儲存體連結服務。 本文假設您使用相同的 Azure 儲存體帳戶。
    - **PackageFile** 設為 **customactivitycontainer/Mydotnetactivity.zip**。 它的格式 ︰ <containerforthezip>/ < nameofthezip.zip >。
    - 自訂活動會採用 **InputDataset** 做為輸入和 **OutputDataset** 做為輸出。
    - 自訂活動的 linkedServiceName 屬性指向 **HDInsightLinkedService**, ，這會告知自訂活動必須在 Azure HDInsight 叢集上執行的 Azure Data Factory。
    - **isPaused** 屬性設定為 **false** 預設。 在此範例中，管線會立即執行，因為配量已在過去開始。 您可以將此屬性設為 true，以暫停管線，並將其設回 false，以重新啟動。 
    -  **啟動** 時間和 **結束** 時間 **5** 小時切割和配量是每小時產生，因此 5 個配量由管線所產生。 


4. 按一下 [ **部署** 命令列來部署管線。

### 監視管線
 
8. 在 Azure 入口網站中 [Data Factory] 分頁中，按一下 [ **圖表**。
    
    ![[圖表] 磚](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
 
9. 在 [圖表] 檢視中，現在按一下 OutputDataset。
 
    ![圖表檢視](./media/data-factory-use-custom-activities/diagram.png)

10. 如果已產生 5 個輸出配量，您應該會看到它們都處於就緒狀態。

    ![輸出配量](./media/data-factory-use-custom-activities/OutputSlices.png)
    
12. 確認 blob 儲存體中會產生輸出檔案 **adftutorial** 容器。

    ![自訂活動的輸出][image-data-factory-ouput-from-custom-activity]

9. 如果您開啟輸出檔，您應該會看到類似下面的輸出：

    檔案 inputfolder/2015-11-16-00/file.txt 中找到搜尋詞彙 "Microsoft" 出現 2 次。

10. 使用 [Azure 入口網站][azure-preview-portal] 或 Azure PowerShell cmdlet 來監視您的資料處理站、 管線和資料集。 您可以查看來自訊息 **ActivityLogger** 中的記錄檔 (特別是 0.log 使用者)，您可以從入口網站或使用指令程式的自訂活動的程式碼中。

    ![從自訂活動下載記錄檔][image-data-factory-download-logs-from-custom-activity]


請參閱 [監視和管理管線](data-factory-monitor-manage-pipelines.md) 監視資料集和管線的詳細步驟。      

### 偵錯管線
偵錯包含一些基本技術：

1.  如果輸入配量不設定為 **準備**, ，確認輸入的資料夾結構正確，且 file.txt 存在輸入資料夾中。
2.  在 **Execute** 方法的自訂活動，使用 **IActivityLogger** 記錄資訊可協助您疑難排解問題的物件。 記錄的訊息會顯示在 user_0.log 檔案中。 

    在 **OutputDataset** 刀鋒視窗中，按一下配量，以查看 **資料配量** 該配量分頁。 您會看到 **活動執行** 該配量。 您會看到一個為該配量執行的活動。 如果您按一下命令列中的 [執行]，您可以為相同的配量啟動另一個活動執行。 

    當您按一下活動執行時，您會看到 **活動執行詳細資料** 刀鋒視窗中的記錄檔清單。 您會在 user_0.log 檔案中看到記錄的訊息。 發生錯誤時，您會看到三個活動執行，因為管線/活動 JSON 中的重試計數設定為 3。 當您按一下活動執行，您會看到您可以檢閱的記錄檔來疑難排解錯誤。 

    在記錄檔的清單中，按一下 [ **使用者 0.log**。 在右窗格中所使用的結果 **IActivityLogger.Write** 方法。

    您也應該檢查 **系統 0.log** 任何系統錯誤訊息和例外狀況。

3.  包含 **PDB** 檔案的 zip 檔案中，所以錯誤詳細資料會有資訊例如 **呼叫堆疊** 發生的錯誤。
4.  自訂活動 zip 檔案中的所有檔案都必須位於 **上層** 與任何子資料夾。
5.  請確認 **assemblyName** (MyDotNetActivity.dll)， **進入點**(MyDotNetActivityNS.MyDotNetActivity)， **packageFile** (customactivitycontainer/Mydotnetactivity.zip) 和 **packageLinkedService** （應該指向包含 zip 檔案的 Azure blob 儲存體） 設定為正確的值。 
6.  如果您修正錯誤，並想要重新處理配量，以滑鼠右鍵按一下配量處於 **OutputDataset** 分頁，然後按一下 **執行**。 


## 更新自訂活動
如果您更新自訂活動的程式碼，請建置它，並將包含新二進位檔案的 zip 檔案上傳至 Blob 儲存體。

## <a name="AzureBatch"></a> 使用 Azure Batch 連結服務
> [AZURE.NOTE] 請參閱 [Azure 批次的基本概念][batch-technical-overview] 服務 Azure 批次的概觀，請參閱 [開始使用.NET 的 Azure 批次程式庫][batch-get-started] 來快速地開始使用 Azure Batch 服務。

您可以將 Azure Batch 當作計算資源使用，執行您的自訂 .NET 活動。 您必須建立自己的 Azure Batch 集區，並指定 VM 數量和其他組態。 Azure Batch 集區為客戶提供下列功能：

1. 建立包含一至數千個核心的集區。
2. 根據公式自動調整 VM 數量
3. 支援任何大小的 VM
4. 每個 VM 均可設定工作數目
5. 佇列無限個工作


以下是上一節所述的逐步解說中使用 Azure Batch 連結服務的高階步驟：

1. 建立 Azure 批次帳戶使用 [Azure 入口網站](http://manage.windowsazure.com)。 請參閱 [建立和管理 Azure 批次帳戶][batch-create-account] 文章的指示。 記下 Azure Batch 帳戶名稱和帳戶金鑰。

    您也可以使用 [New-azurebatchaccount][new-azure-batch-account] cmdlet 來建立 Azure Batch 帳戶。 請參閱 [使用 Azure PowerShell 管理 Azure 批次帳戶][azure-batch-blog] 如需使用這個指令程式的詳細指示。
2. 建立 Azure Batch 集區。 您可以下載的程式碼 [Azure Batch 總管工具][batch-explorer], 、 編譯和使用它 （或） 使用 [適用於.NET 的 Azure 批次程式庫][batch-net-library] 來建立 Azure Batch 集區。 請參閱 [Azure Batch 總管範例逐步解說][batch-explorer-walkthrough] 如需使用 Azure Batch 總管的逐步指示。

    您也可以使用 [新增 AzureRmBatchPool](https://msdn.microsoft.com/library/mt628690.aspx) cmdlet 來建立 Azure Batch 集區。

    您可能會想要使用至少 2 個計算節點建立 Azure Batch 集區，以便以平行方式處理配量。 如果您使用 Batch 總管：  

    - 請輸入集區的識別碼 (**集區識別碼**)。 請注意 **的集區識別碼**; 您將在建立 Data Factory 方案時需要它。 
    - 指定 **Windows Server 2012 R2** 作業系統系列] 設定。
    - 指定 **2** 值為 **每個計算節點的最大工作** 設定。
    - 指定 **2** 值為 **數字的目標專用** 設定。 

    Data Factory 服務在 Azure 批次中建立工作名稱 ︰ adf-<pool name>︰ 工作 xxx。 配量的每個活動執行會建立一個工作。 如果有 10 個配量就緒可供處理，此作業中會建立 10 個作業。 如果您在集區中有多個計算結點，您可以同時執行多個配量。 如果每個計算結點的最大工作設為 > 1，您也可以在相同的計算中執行多個配量。 
    
    ![Batch 總管工作](./media/data-factory-use-custom-activities/BatchExplorerTasks.png)

    ![Data Factory & Batch](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

2. 使用下列 JSON 範本建立 Azure Batch 連結服務。 Data Factory 編輯器會顯示類似的範本讓您開始使用。 JSON 片段中指定的 Azure Batch 帳戶名稱、帳戶金鑰和集區名稱。

        {
          "name": "AzureBatchLinkedService",
          "properties": {
            "type": "AzureBatch",
            "typeProperties": {
              "accountName": "<Azure Batch account name>",
              "batchUri": "https://<region>.batch.azure.com",
              "accessKey": "<Azure Batch account key>",
              "poolName": "<Azure Batch pool name>",
              "linkedServiceName": "<Specify associated storage linked service reference here>"
            }
          }
        }

    > [AZURE.IMPORTANT]  **URL** 從 **Azure 批次帳戶] 刀鋒視窗** 採用下列格式 ︰ accountname.region.batch.azure.com。 如 **batchUri** 在 JSON 中的屬性，您必須 **移除 「 帳戶名稱 」。** 使用 URL 與 **accountname** 的 **accountName** JSON 屬性。

    如 **poolName** 屬性，您也可以指定的集區，而不是集區的名稱識別碼。

    請參閱 [Azure Batch 連結服務 MSDN 主題](https://msdn.microsoft.com/library/mt163609.aspx) 如需這些屬性的描述。

2.  在 Data Factory 編輯器中，開啟您在取代與逐步解說中建立的管線的 JSON 定義 **HDInsightLinkedService** 與 **AzureBatchLinkedService**。
3.  您可能希望變更管線的開始和結束時間，讓您能夠使用 Azure Batch 服務來測試案例。
4.  在 Azure Batch 總管中，您可以看到與處理配量相關聯的 Azure Batch 工作，如下圖所示。

    ![Azure Batch 工作][image-data-factory-azure-batch-tasks]

> [AZURE.NOTE] Data Factory 服務不支援隨選項 Azure 批次，但 HDInsight。 您只能使用 Azure Data Factory 中自己的 Azure Batch 集區。    

## 另請參閱

[Azure Data Factory 更新 ︰ 執行 ADF 自訂.NET 活動使用 Azure Batch](http://azure.microsoft.com/blog/2015/05/01/azure-data-factory-updates-execute-adf-custom-net-activities-using-azure-batch/)。

[batch-net-library]: ../batch/batch-dotnet-get-started.md
[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[batch-create-account]: ../batch/batch-account-create-portal.md
[batch-technical-overview]: ../batch/batch-technical-overview.md
[batch-get-started]: ../batch/batch-dotnet-get-started.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[azure-powershell-install]: https://github.com/Azure/azure-sdk-tools/releases


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[new-azure-batch-account]: https://msdn.microsoft.com/library/mt125880.aspx
[new-azure-batch-pool]: https://msdn.microsoft.com/library/mt125936.aspx
[azure-batch-blog]: http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx

[nuget-package]: http://go.microsoft.com/fwlink/?LinkId=517478
[azure-developer-center]: http://azure.microsoft.com/develop/net/
[adf-developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-get-started.md
[hivewalkthrough]: data-factory-data-transformation-activities.md

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png

[image-data-factory-azure-batch-tasks]: ./media/data-factory-use-custom-activities/AzureBatchTasks.png


