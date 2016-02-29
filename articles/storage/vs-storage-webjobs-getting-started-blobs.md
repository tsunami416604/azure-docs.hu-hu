<properties
    pageTitle="開始使用 Blob 儲存體和 Visual Studio 已連接服務 (WebJob 專案) | Microsoft Azure"
    description="在使用 Visual Studio 已連接服務連接至 Azure 儲存體之後，如何於 WebJob 專案中開始使用 Blob 儲存體。"
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-getting-started"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/16/2015"
    ms.author="tarcher"/>

# 開始使用 Azure Blob 儲存體和 Visual Studio 已連接服務 (WebJob 專案)

## 概觀

本文提供 C# 程式碼範例，示範如何在建立或更新 Azure Blob 時觸發程序。 此程式碼範例使用 [WebJobs SDK](websites-dotnet-webjobs-sdk.md) 版本 1.x。 當您儲存體帳戶加入 WebJob 專案使用 Visual Studio **新增連接的服務** ] 對話方塊中，適當的 Azure 儲存體 NuGet 封裝已安裝、 適當的.NET 參考會加入至專案，和儲存體帳戶的連接字串會在 App.config 檔案中更新。



## 如何在建立或更新 Blob 時觸發函數

本節說明如何使用 **BlobTrigger** 屬性。

 **注意:** WebJobs SDK 會掃描要監看新的或已變更 blob 的記錄檔。 此程序的速度原本就很慢；可能直到建立 Blob 之後數分鐘或更久，才會觸發函數。  如果您的應用程式需要立即處理 blob，建議的方法是建立佇列訊息，當您建立 blob，並使用 [QueueTrigger](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#trigger) 屬性而不是 **BlobTrigger** 處理 blob 的函數上的屬性。

### 適用於含有副檔名之 Blob 名稱的單一預留位置  

下列程式碼範例複製中出現的文字 blob *輸入* 容器 *輸出* 容器:

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("output/{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

屬性建構函式會接受字串參數，以指定容器名稱和 Blob 名稱的預留位置。 在此範例中，如果名為 *Blob1.txt* 中建立 *輸入* 容器，函式會建立名為的 blob *Blob1.txt* 中 *輸出* 容器。

您可以使用 Blob 名稱預留位置來指定名稱模式，如下列程式碼範例所示：

        public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
            [Blob("output/copy-{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

此程式碼只會複製以 "original-" 做為名稱開頭的 Blob。 例如， *原始 Blob1.txt* 中 *輸入* 容器複製到 *複製 Blob1.txt* 中 *輸出* 容器。

如果您需要針對名稱中包含大括號的 Blob 名稱指定名稱模式，請按兩下大括號。 例如，如果您想要尋找中的 blob *映像* 具備如下名稱的容器:

        {20140101}-soundfile.mp3

針對您的模式使用下一行程式碼：

        images/{{20140101}}-{name}

在範例中， *名稱* 預留位置的值會是 *soundfile.mp3*。

### 個別的 Blob 名稱和副檔名預留位置

下列程式碼範例會變更檔案的副檔名，會出現在 blob 複製 *輸入* 容器 *輸出* 容器。 程式碼會記錄的延伸模組 *輸入* blob，然後設定的延伸模組 *輸出* blob 到 *.txt*。

        public static void CopyBlobToTxtFile([BlobTrigger("input/{name}.{ext}")] TextReader input,
            [Blob("output/{name}.txt")] out string output,
            string name,
            string ext,
            TextWriter logger)
        {
            logger.WriteLine("Blob name:" + name);
            logger.WriteLine("Blob extension:" + ext);
            output = input.ReadToEnd();
        }

## 您可以繫結至 Blob 的類型

您可以使用 **BlobTrigger** 下列類型的屬性:

* **字串**
* **TextReader**
* **Stream**
* **ICloudBlob**
* **CloudBlockBlob**
* **CloudPageBlob**
* 還原序列化其他類型 [ICloudBlobStreamBinder](#icbsb)

如果您想要直接使用 Azure 儲存體帳戶，您也可以加入 **CloudStorageAccount** 方法簽章的參數。

## 繫結至字串來取得文字 Blob 內容

如果預期會取得文字 blob， **BlobTrigger** 可以套用至 **字串** 參數。 下列程式碼範例會將文字 blob 繫結 **字串** 參數命名為 **logMessage**。 此函數會使用該參數，將 Blob 的內容寫入 WebJobs SDK 儀表板。

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## 使用 ICloudBlobStreamBinder 來取得序列化的 Blob 內容

下列程式碼範例會使用一個類別，實作 **ICloudBlobStreamBinder** 啟用 **BlobTrigger** 屬性繫結至 blob **WebImage** 型別。

        public static void WaterMark(
            [BlobTrigger("images3/{name}")] WebImage input,
            [Blob("images3-watermarked/{name}")] out WebImage output)
        {
            output = input.AddTextWatermark("WebJobs SDK",
                horizontalAlign: "Center", verticalAlign: "Middle",
                fontSize: 48, opacity: 50);
        }
        public static void Resize(
            [BlobTrigger("images3-watermarked/{name}")] WebImage input,
            [Blob("images3-resized/{name}")] out WebImage output)
        {
            var width = 180;
            var height = Convert.ToInt32(input.Height * 180 / input.Width);
            output = input.Resize(width, height);
        }

 **WebImage** 繫結程式碼中提供 **WebImageBinder** 類別衍生自 **ICloudBlobStreamBinder**。

        public class WebImageBinder : ICloudBlobStreamBinder<WebImage>
        {
            public Task<WebImage> ReadFromStreamAsync(Stream input,
                System.Threading.CancellationToken cancellationToken)
            {
                return Task.FromResult<WebImage>(new WebImage(input));
            }
            public Task WriteToStreamAsync(WebImage value, Stream output,
                System.Threading.CancellationToken cancellationToken)
            {
                var bytes = value.GetBytes();
                return output.WriteAsync(bytes, 0, bytes.Length, cancellationToken);
            }
        }

## 如何處理有害的 Blob

當 **BlobTrigger** 函數失敗時，SDK 會再次呼叫它，以防失敗因暫時性錯誤所造成。 如果失敗是因為 Blob 的內容所造成，則此函數會在其每次嘗試處理該 Blob 時失敗。 根據預設，SDK 最多會針對指定的 Blob 呼叫函數 5 次。 如果第五次嘗試失敗，SDK 會將訊息加入佇列，名為 *webjobs-blobtrigger-有害*。

您可以設定重試次數上限。 相同 [MaxDequeueCount](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#configqueue) 設定用於處理有害的 blob 和佇列的有害訊息處理。

適用於有害 Blob 的佇列訊息是一個 JSON 物件，其中包含下列屬性：

* FunctionId (格式 *{WebJob name}*。函式。*{Function name}*, ，例如: WebJob1.Functions.CopyBlob)
* BlobType ("BlockBlob" 或 "PageBlob")
* ContainerName
* BlobName
* ETag (Blob 版本識別碼，例如："0x8D1DC6E70A277EF")

在下列程式碼範例中， **CopyBlob** 函數具有會導致每次呼叫時失敗的程式碼。 之後 SDK 呼叫它的重試次數上限，在有害的 blob 佇列中，建立一則訊息，而且該訊息會由 **LogPoisonBlob** 函式。

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("textblobs/output-{name}")] out string output)
        {
            throw new Exception("Exception for testing poison blob handling");
            output = input.ReadToEnd();
        }

        public static void LogPoisonBlob(
        [QueueTrigger("webjobs-blobtrigger-poison")] PoisonBlobMessage message,
            TextWriter logger)
        {
            logger.WriteLine("FunctionId: {0}", message.FunctionId);
            logger.WriteLine("BlobType: {0}", message.BlobType);
            logger.WriteLine("ContainerName: {0}", message.ContainerName);
            logger.WriteLine("BlobName: {0}", message.BlobName);
            logger.WriteLine("ETag: {0}", message.ETag);
        }

SDK 會自動將該 JSON 訊息還原序列化。 以下是 **PoisonBlobMessage** 類別:

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### Blob 輪詢演算法

WebJobs SDK 會掃描所指定的所有容器 **BlobTrigger** 屬性在應用程式啟動。 在大型儲存體帳戶中這個掃描需要花費一些時間，因此，可能需要一段時間才能找到新的 blob 和 **BlobTrigger** 函式會執行。

為了在應用程式啟動之後偵測新的或已變更的 Blob，SDK 會定期讀取 Blob 儲存體記錄檔。 Blob 記錄檔會進行緩衝處理，並只進行實際寫入每隔 10 分鐘，因此可能會有明顯的延遲後建立或更新在對應的 blob，或 **BlobTrigger** 函式會執行。

發生例外狀況，您可以使用建立的 blob **Blob** 屬性。 當 WebJobs SDK 建立新的 blob 時，會將新的 blob 立即傳遞到任何相符 **BlobTrigger** 函式。 因此，如果您具有 Blob 輸入和輸出的鏈結，就能有效率地處理它們。 但是，如果您想要執行 blob 處理函數時所建立，或透過其他方式更新 blob 的低延遲，建議使用 **QueueTrigger** 而 **BlobTrigger**。

### Blob 回條

WebJobs SDK 可確保沒有 **BlobTrigger** 函式會針對相同的新的或更新的 blob 呼叫一次以上。 做法是維護 *blob 回條* 以判斷指定的 blob 版本是否已處理過。

Blob 回條會儲存在名為的容器 *azure webjobs 託管* AzureWebJobsStorage 連接字串所指定的 Azure 儲存體帳戶中。 Blob 回條具有下列資訊:

* Blob 已呼叫的函式 (「*{WebJob name}*。函式。*{Function name}*」，例如:"WebJob1.Functions.CopyBlob")
* 容器名稱
* Blob 類型 ("BlockBlob" 或 "PageBlob")
* Blob 名稱
* ETag (Blob 版本識別碼，例如："0x8D1DC6E70A277EF")

如果您想要強制重新處理某個 blob，您可以手動刪除該 blob 的 blob 回條 *azure webjobs 託管* 容器。

## 佇列文章所涵蓋的相關主題

如需如何處理佇列訊息，所觸發的 blob 處理的詳細資訊，或 WebJobs sdk 案例特有 blob 處理，請參閱 [如何透過 WebJobs SDK 使用 Azure 佇列儲存體](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)。

該文章中涵蓋的相關主題如下：

* Async 函數
* 多個執行個體
* 正常關機
* 在函式主體中使用 WebJobs SDK 屬性
* 在程式碼中設定 SDK 連接字串。
* 在程式碼中設定 WebJobs SDK 建構函式參數的值
* 設定 **MaxDequeueCount** 來處理有害的 blob。
* 手動觸發函式
* 寫入記錄檔

## 後續步驟

本文提供的程式碼範例示範如何處理使用 Azure Blob 的常見案例。 如需如何使用 Azure WebJobs 和 WebJobs SDK 的詳細資訊，請參閱 [Azure WebJobs 建議資源](http://go.microsoft.com/fwlink/?linkid=390226)。

