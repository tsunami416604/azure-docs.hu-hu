<properties
    pageTitle="Hyperlapse Media 檔案與 Azure Media Hyperlapse"
    description="Azure Media Hyperlapse 能夠利用第一人稱視角或運動攝影的內容，來建立流暢的縮時影片。 本主題說明如何使用 Media Indexer。"
    services="media-services"
    documentationCenter=""
    authors="asolanki"
    manager="johndeu"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/19/2015"   
    ms.author="adsolank"/>


# Hyperlapse Media 檔案與 Azure Media Hyperlapse

Azure Media Hyperlapse 是可以使用第一人稱視角或運動攝影機內容建立流暢縮時攝影影片的「媒體處理器 (MP)」。  以雲端為基礎的同層級 [Microsoft Research 桌面 Hyperlapse Pro 和電話型 Hyperlapse Mobile](http://aka.ms/hyperlapse), ，Microsoft Hyperlapse 的 Azure 媒體服務會利用 Azure 媒體服務媒體處理平台的水平調整和平行處理大規模大量 Hyperlapse 處理。

>[AZURE.IMPORTANT] Azure 媒體服務的 Microsoft Hyperlapse 是免費的公用預覽狀態。  工作限制在輸入資產的前 10,000 個畫面。


> Microsoft Hyperlapse 是專門使用移動中攝影機拍攝第一人稱視角內容而設計。  雖然攝影機位置固定的內容也可以運作，但 Azure 媒體 Hyperlapse 媒體處理器無法保證其他類型內容的效能及品質。  若要深入了解 Microsoft Hyperlapse Azure 媒體服務，並查看一些範例影片，請參閱 [簡介的部落格文章](http://aka.ms/azurehyperlapseblog) 從公開預覽版本中。

Azure 媒體 Hyperlapse 工作接受輸入 MP4、MOV 或 WMV 資產檔案連同組態檔，以指定影片中要縮時的畫面及其速度 (例如前 10,000 個畫面速度為 2x)。  輸出是輸入影片經過穩定和縮時轉譯的成果。

Azure Media Hyperlapse 的最新更新，請參閱 [媒體服務部落格](http://azure.microsoft.com/blog/topics/media-services/)。

## 將資產進行 Hyperlapse 處理

首先您需要上傳要輸入 Azure 媒體服務的檔案。  若要瞭解有關上傳及管理內容的相關概念的詳細資訊，請參閱 [內容管理文章](media-services-manage-content.md#upload)。

###  <a id="configuration"></a>Hyperlapse 組態預設值

一旦內容在媒體服務帳戶中，您將需要建構預設組態。  下表說明使用者指定的欄位：

 欄位 | 說明
-------|-------------
StartFrame|開始進行 Microsoft Hyperlapse 處理的畫面。
NumFrames|要處理的畫面數目。
速度|輸入影片要加速的設定因素。

以下是符合標準的 JSON 和 XML 格式組態檔：

**XML 預設：**

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
        <Sources>
            <Source StartFrame="0" NumFrames="10000" />
        </Sources>
        <Options>
            <Speed>12</Speed>
        </Options>
    </Preset>

**JSON 預設：**

    {
        "Version":1.0,
        "Sources": [
            {
                "StartFrame":0,
                "NumFrames":2147483647
            }
        ],
        "Options": {
            "Speed":1,
            "Stabilize":false
        }
    }

###  <a id="sample_code"></a> Microsoft Hyperlapse AMS.NET sdk

下列方法會將媒體檔案上傳為資產，並使用 Azure 媒體 Hyperlapse 媒體處理器建立工作。  

> [AZURE.NOTE] 您應該已經 CloudMediaContext 範圍中具有名稱 [內容] 此程式碼才能運作。  若要深入了解，閱讀 [內容管理文章](media-services-manage-content.md)。

> [AZURE.NOTE] 字串引數"hyperConfig"應該符合組態中 JSON 或 XML 預設，上面所述。

    static bool RunHyperlapseJob(string input, string output, string hyperConfig)
    {
        // create asset with input file
        IAsset asset = context
                       .Assets
                       .CreateAssetAndUploadSingleFile(input, "My Hyperlapse Input", AssetCreationOptions.None);

        // grab instances of Azure Media Hyperlapse MP
        IMediaProcessor mp = context
                             .MediaProcessors
                             .GetLatestMediaProcessorByName("Azure Media Hyperlapse");

        // create Job with Hyperlapse task
        IJob job = context
                   .Jobs
                   .Create(String.Format("Hyperlapse {0}", input));

        if (String.IsNullOrEmpty(hyperConfig))
        {
            // config cannot be empty
            return false;
        }

        hyperConfig = File.ReadAllText(hyperConfig);

        ITask hyperlapseTask = job.Tasks.AddNew("Hyperlapse task",
                                                mp,
                                                hyperConfig,
                                                TaskOptions.None);
        hyperlapseTask.InputAssets.Add(asset);
        hyperlapseTask.OutputAssets.AddNew("Hyperlapse output",
                                            AssetCreationOptions.None);


        job.Submit();

        // Create progress printing and querying tasks
            Task progressPrintTask = new Task(() =>
            {

                IJob jobQuery = null;
                do
                {
                    var progressContext = context;
                    jobQuery = progressContext.Jobs
                                              .Where(j => j.Id == job.Id)
                                              .First();
                    Console.WriteLine(string.Format("{0}\t{1}\t{2}",
                                      DateTime.Now,
                                      jobQuery.State,
                                      jobQuery.Tasks[0].Progress));
                    Thread.Sleep(10000);
                }
                while (jobQuery.State != JobState.Finished &&
                       jobQuery.State != JobState.Error &&
                       jobQuery.State != JobState.Canceled);
            });
            progressPrintTask.Start();

            Task progressJobTask = job.GetExecutionProgressTask(
                                                 CancellationToken.None);
            progressJobTask.Wait();

            // If job state is Error, the event handling
            // method for job progress should log errors.  Here we check
            // for error state and exit if needed.
            if (job.State == JobState.Error)
            {
                ErrorDetail error = job.Tasks.First().ErrorDetails.First();
                Console.WriteLine(string.Format("Error: {0}. {1}",
                                                error.Code,
                                                error.Message));  
                return false;                  
            }

        DownloadAsset(job.OutputMediaAssets.First(), output);
        return true;
    }

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }


    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = context.Assets.Create(assetName, options);

        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);

        return asset;
    }

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }

### <a id="file_types"></a>支援的檔案類型

- MP4
- MOV
- WMV



##媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]





<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

