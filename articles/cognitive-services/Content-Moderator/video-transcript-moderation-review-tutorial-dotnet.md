---
title: 'Oktatóanyag: Mérsékelt videók és a .NET - Content Moderator szövegekben'
titlesuffix: Azure Cognitive Services
description: Ez az oktatóanyag segítségével megismerheti, hogyan hozhat létre egy teljes videó és a szöveges moderálás megoldás a gépi támogatású képmoderálás és emberi hurok felülvizsgálat létrehozása.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: e3578c08b78894c2f9a858e97c7cbe2d260731c5
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878748"
---
# <a name="tutorial-video-and-transcript-moderation"></a>Oktatóanyag: A videó és a szöveges moderálása

A Content Moderator video API-jaival az emberi vizsgálóeszközben moderálhat videókat és hozhat létre videoértékeléseket. 

Ez az oktatóanyag segítségével megismerheti, hogyan hozhat létre egy teljes videó és a szöveges moderálás megoldás a gépi támogatású képmoderálás és emberi hurok felülvizsgálat létrehozása.

Az oktatóanyaghoz töltse le a [C# konzolalkalmazást](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp). A konzolalkalmazás az SDK-val és a kapcsolódó csomagokkal végzi el a következő feladatokat:

- A bemeneti videó(k) tömörítése gyorsabb feldolgozáshoz
- A videó moderálása jelenetek és képkockák elemzéséhez
- Miniatűrök (képek) létrehozása időbélyegekkel
- Videoértékelések készítése időbélyegek és miniatűrök beküldésével
- A videó szövegének írott szöveggé alakítása (átiratkészítés) a Media Indexer API-val
- Az átirat moderálása a szövegmoderálási szolgáltatással
- A moderált átiratok hozzáadása a videoértékeléshez

## <a name="sample-program-outputs"></a>Programkimenetek mintái

Mielőtt továbblép, a program a következő minta kimenetek vizsgáljuk meg:

- [Konzolkimenet](#program-output)
- [Videoértékelés](#video-review-default-view)
- [Átiratértékelés](#video-review-transcript-view)

## <a name="prerequisites"></a>Előfeltételek

1. Regisztráljon a [Content Moderator felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com/) webhelyén, és [hozzon létre egyéni címkéket](Review-Tool-User-Guide/tags.md), amelyeket a C# konzolalkalmazás társít a kódból. Az alábbi képernyőfelvételen az egyéni címkék láthatók.

  ![Videomoderálás – egyéni címkék](images/video-tutorial-custom-tags.png)

1. A mintaalkalmazás futtatásához egy Azure-fiókra és egy Azure Media Services-fiókra lesz szüksége. Emellett hozzá kell tudnia férni a Content Moderator privát előzetes verziójához. Végezetül Azure Active Directory-hitelesítő adatokra lesz szüksége. További információt ezen adatok megszerzéséről a [Video Moderation API gyors útmutatójában](video-moderation-api.md) találhat.

1. Szerkessze a fájlt (`App.config`), majd adja hozzá az Active Directory-bérlő nevét, a szolgáltatásvégpontokat, és a `#####` jellel jelölt előfizetési kulcsokat. A következő adatokra lesz szüksége:

|Kulcs|Leírás|
|-|-|
|`AzureMediaServiceRestApiEndpoint`|Az Azure Media Services (AMS) API végpontja|
|`ClientSecret`|Az Azure Media Services előfizetési kulcsa|
|`ClientId`|Az Azure Media Services ügyfélazonosítója|
|`AzureAdTenantName`|A szervezetet képviselő Active Directory-bérlő neve|
|`ContentModeratorReviewApiSubscriptionKey`|A Content Moderator felügyeleti API előfizetési kulcsa|
|`ContentModeratorApiEndpoint`|A Content Moderator API végpontja|
|`ContentModeratorTeamId`|A tartalommoderátori csapat azonosítója|

## <a name="getting-started"></a>Első lépések

A `Program` osztály a `Program.cs` fájlban a videomoderálási alkalmazás fő belépési pontja.

### <a name="methods-of-class-program"></a>A Program osztály metódusai

|Módszer|Leírás|
|-|-|
|`Main`|Elemzi a parancssort, felhasználói adatokat gyűjt, és megkezdi a feldolgozást.|
|`ProcessVideo`|Videoértékeléseket tömörít, tölt fel és moderál.|
|`CreateVideoStreamingRequest`|Videofeltöltési streamet hoz létre|
|`GetUserInputs`|Felhasználói adatokat gyűjt; hiányzó parancssori opciók esetén használandó|
|`Initialize`|Inicializálja a moderálási folyamathoz szükséges objektumokat|

### <a name="the-main-method"></a>A Main módszer

A `Main()` a végrehajtás első fázisa, így a videomoderálási folyamat ismertetését is ezzel kezdjük.

    static void Main(string[] args)
    {
        if (args.Length == 0)
        {
            string videoPath = string.Empty;
            GetUserInputs(out videoPath);
            Initialize();
            AmsConfigurations.logFilePath = Path.Combine(Path.GetDirectoryName(videoPath), "log.txt");
            try
            {
                ProcessVideo(videoPath).Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }
        else
        {
            DirectoryInfo directoryInfo = new DirectoryInfo(args[0]);
            if (args.Length == 2)
                bool.TryParse(args[1], out generateVtt);
            Initialize();
            AmsConfigurations.logFilePath = Path.Combine(args[0], "log.txt");
            var files = directoryInfo.GetFiles("*.mp4", SearchOption.AllDirectories);
            foreach (var file in files)
            {
                try
                {
                    ProcessVideo(file.FullName).Wait();
                }
                catch (Exception ex)
                {
                    Console.WriteLine(ex.Message);
                }
            }
        }
    }

A `Main()` a következő parancssori argumentumokat kezeli:

- A moderálásra váró MPEG-4 videofájlokat tartalmazó könyvtár elérési útvonala. A könyvtárban és az alkönyvtáraiban található minden `*.mp4` fájl moderálásra kerül.
- Választható egy logikai (igaz/hamis) jelölő, amely jelzi, hogy szükséges-e szöveges átiratok készítése a hangmoderáláshoz.

Ha nincs parancssori argumentum, a `Main()` meghívja a `GetUserInputs()` parancsot. Ez a metódus arra kéri a felhasználót, hogy adja meg egy videofájl elérési útvonalát, valamint szabja meg, hogy készüljön-e szöveges átirat.

> [!NOTE]
> A konzolalkalmazás az [Azure Media Indexer API-val](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) átiratokat készít a feltöltött videó hangsávjából. Az eredmények WebVTT formátumban készülnek el. További információt erről a formátumról a [webes videók szövegsávos formátumáról](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API) szóló cikkben találhat.

### <a name="initialize-and-processvideo-methods"></a>Inicializálás és ProcessVideo-módszerek

Attól függetlenül, hogy a programbeállítások a parancssorból vagy interaktív felhasználói parancsokból származnak, a `Main()` következő lépésben az `Initialize()` parancsot hívja az alábbi példányok létrehozásához:

|Osztály|Leírás|
|-|-|
|`AMSComponent`|Tömöríti a videofájlokat, majd moderálásra küldi őket.|
|`AMSconfigurations`|Az alkalmazás konfigurációs adatainak kezelőfelülete, amely az `App.config` fájlban található.|
|`VideoModerator`| Feltöltés, kódolás, titkosítás és moderálás az AMS SDK-val|
|`VideoReviewApi`|A videoértékeléseket felügyeli a Content Moderator szolgáltatásban|

Ezekről az osztályokról (az `AMSConfigurations` osztályt leszámítva, amely magától értetődő) részletesebb információt az oktatóanyag következő szakaszaiban találhat.

A program a videofájlokat végül egyenként, a `ProcessVideo()` paranccsal dolgozza fel.

    private static async Task ProcessVideo(string videoPath)
    {
        Stopwatch sw = new Stopwatch();
        sw.Start();
        Console.ForegroundColor = ConsoleColor.White;
        Console.WriteLine("\nVideo compression process started...");

        var compressedVideoPath = amsComponent.CompressVideo(videoPath);
        if (string.IsNullOrWhiteSpace(compressedVideoPath))
        {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.WriteLine("Video Compression failed.");
        }

        Console.WriteLine("\nVideo compression process completed...");

        UploadVideoStreamRequest uploadVideoStreamRequest = CreateVideoStreamingRequest(compressedVideoPath);
        UploadAssetResult uploadResult = new UploadAssetResult();

        if (generateVtt)
        {
            uploadResult.GenerateVTT = generateVtt;
        }
        Console.WriteLine("\nVideo moderation process started...");

        if (!videoModerator.CreateAzureMediaServicesJobToModerateVideo(uploadVideoStreamRequest, uploadResult))
        {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.WriteLine("Video Review process failed.");
        }

        Console.WriteLine("\nVideo moderation process completed...");
        Console.WriteLine("\nVideo review process started...");
        string reviewId = await videoReviewApi.CreateVideoReviewInContentModerator(uploadResult);
        Console.WriteLine("\nVideo review successfully completed...");
        sw.Stop();
        Console.WriteLine("\nTotal Elapsed Time: {0}", sw.Elapsed);
        using (var stw = new StreamWriter(AmsConfigurations.logFilePath, true))
        {
            stw.WriteLine("Video File Name: " + Path.GetFileName(videoPath));
            stw.WriteLine($"ReviewId: {reviewId}");
            stw.WriteLine("Total Elapsed Time: {0}", sw.Elapsed);
        }
    }


A `ProcessVideo()` metódus egyszerűen működik. A következő műveleteket hajtja végre, ebben a sorrendben:

- Tömöríti a videót
- Feltölti a videót egy Azure Media Services-eszközre
- Létrehoz egy AMS-feladatot a videó moderálásához
- Létrehoz egy videóértékelést a Content Moderator szolgáltatásban

A következő szakaszok a `ProcessVideo()` által meghívott folyamatokat részletezik. 

## <a name="compressing-the-video"></a>A videó tömörítése

A hálózati forgalom minimalizálása érdekében az alkalmazás H.264 (MPEG-4 AVC) formátumba konvertálja a videofájlokat, és 640 képpontos maximum szélességűre méretezi őket. A H.264 kodek rendkívül hatékony (magas tömörítési aránnyal bír), ezért javasolt a használata. A tömörítés az ingyenes `ffmpeg` parancssori eszközzel történik, amely a Visual Studio szolgáltatás `Lib` mappájában található. A bemeneti fájlok bármilyen, `ffmpeg` által támogatott formátumúak lehetnek, beleértve a leggyakrabban használt videoformátumokat és kodekeket.

> [!NOTE]
> Amikor a parancssorral indítja el a programot, megadja a videofájlokat tartalmazó könyvtárat, amelyet moderálásra küld. A program a könyvtár összes, `.mp4` kiterjesztésű fájlját feldolgozza. A fájlkiterjesztések feldolgozásához frissítse úgy a `Program.cs` `Main()` metódusát, hogy az tartalmazza a kívánt bővítményeket.

Az egyetlen videofájlt tömörítő kód az `AMSComponent.cs` `AmsComponent` osztálya. Ezért a funkcióért a `CompressVideo()` metódus felelős, amely itt látható.

    public string CompressVideo(string videoPath)
    {
        string ffmpegBlobUrl;
        if (!ValidatePreRequisites())
        {
            Console.WriteLine("Configurations check failed. Please cross check the configurations!");
            throw new Exception();
        }

        if (File.Exists(_configObj.FfmpegExecutablePath))
        {
            ffmpegBlobUrl = this._configObj.FfmpegExecutablePath;
        }
        else
        {
            Console.WriteLine("ffmpeg.exe is missing. Please check the Lib folder");
            throw new Exception();
        }

        string videoFilePathCom = videoPath.Split('.')[0] + "_c.mp4";
        ProcessStartInfo processStartInfo = new ProcessStartInfo();
        processStartInfo.WindowStyle = ProcessWindowStyle.Hidden;
        processStartInfo.FileName = ffmpegBlobUrl;
        processStartInfo.Arguments = "-i \"" + videoPath + "\" -vcodec libx264 -n -crf 32 -preset veryfast -vf scale=640:-1 -c:a aac -aq 1 -ac 2 -threads 0 \"" + videoFilePathCom + "\"";
        var process = Process.Start(processStartInfo);
        process.WaitForExit();
        process.Close();
        return videoFilePathCom;
    }

A kód a következő lépéseket hajtja végre:

- Ellenőrzi, hogy az `App.config` konfigurációja minden szükséges adatot tartalmaz-e
- Ellenőrzi, hogy az `ffmpeg` bináris jelen van-e
- `_c.mp4` a fájl nevéhez való hozzáfűzésével létrehozza a kimeneti fájlnevet (például `Example.mp4` -> `Example_c.mp4`)
- Létrehoz egy parancssori sztringet, amely elvégzi a konvertálást
- Egy `ffmpeg`-folyamatot indít a parancssorral
- Megvárja a videó feldolgozását

> [!NOTE]
> Ha tudja, hogy a videói már H.264 kodekkel vannak tömörítve, és a megfelelő méretűek, átírhatja úgy a `CompressVideo()` parancsot, hogy az kihagyja a tömörítést.

A metódus visszaadja a tömörített kimeneti fájl fájlnevét.

## <a name="uploading-and-moderating-the-video"></a>Videó feltöltése és moderálása

Ahhoz, hogy egy videót feldolgozhasson a Content Moderation szolgáltatással, az Azure Media Servicesben kell tárolni. A `Program.cs` `Program` osztály egy `CreateVideoStreamingRequest()` rövid metódust tartalmaz, amely a videó feltöltéséhez használt streamelési kérelmet képviselő objektumot adja vissza.

    private static UploadVideoStreamRequest CreateVideoStreamingRequest(string compressedVideoFilePath)
    {
        return
            new UploadVideoStreamRequest
            {
                VideoStream = File.ReadAllBytes(compressedVideoFilePath),
                VideoName = Path.GetFileName(compressedVideoFilePath),
                EncodingRequest = new EncodingRequest()
                {
                    EncodingBitrate = AmsEncoding.AdaptiveStreaming
                },
                VideoFilePath = compressedVideoFilePath
            };
    }

A létrejövő `UploadVideoStreamRequest` objektum az `UploadVideoStreamRequest.cs` fájlban van definiálva (a szülője, `UploadVideoRequest`, pedig az `UploadVideoRequest.cs` fájlban). Ezek az osztályok itt nem jelennek meg; rövidek, és egyetlen céljuk a tömörített videók adatainak tárolása. A feltöltési folyamat eredményeit egy másik kizárólag adatokat tároló osztály, az `UploadAssetResult` (`UploadAssetResult.cs`) tartalmazza. Most már képes értelmezni a `ProcessVideo()` sorait:

    UploadVideoStreamRequest uploadVideoStreamRequest = CreateVideoStreamingRequest(compressedVideoPath);
    UploadAssetResult uploadResult = new UploadAssetResult();

    if (generateVtt)
    {
        uploadResult.GenerateVTT = generateVtt;
    }
    Console.WriteLine("\nVideo moderation process started...");

    if (!videoModerator.CreateAzureMediaServicesJobToModerateVideo(uploadVideoStreamRequest, uploadResult))
    {
        Console.ForegroundColor = ConsoleColor.Red;
        Console.WriteLine("Video Review process failed.");
    }

Ezek a sorok a következő feladatokat hajtják végre:

- Létrehoz egy `UploadVideoStreamRequest` parancsot a tömörített videó feltöltéséhez
- Beállítja a kérés `GenerateVTT` jelölőjét, ha a felhasználó szöveges átiratot kért
- Az `CreateAzureMediaServicesJobToModerateVideo()` parancsot meghívva elvégzi a feltöltést és megkapja az eredményt

## <a name="deep-dive-into-video-moderation"></a>A videomoderálás részletes ismertetése

A `CreateAzureMediaServicesJobToModerateVideo()` metódus a `VideoModerator.cs` fájlban található, amely az Azure Media Services szolgáltatással kommunikáló kódok nagy részét tartalmazza. A metódus forráskódja a következő kivonatban látható.

    public bool CreateAzureMediaServicesJobToModerateVideo(UploadVideoStreamRequest uploadVideoRequest, UploadAssetResult uploadResult)
    {
        asset = CreateAsset(uploadVideoRequest);
        uploadResult.VideoName = uploadVideoRequest.VideoName;
        // Encoding the asset , Moderating the asset, Generating transcript in parallel
        IAsset encodedAsset = null;
        //Creates the job for the tasks.
        IJob job = this._mediaContext.Jobs.Create("AMS Review Job");

        //Adding encoding task to job.
        ConfigureEncodeAssetTask(uploadVideoRequest.EncodingRequest, job);

        ConfigureContentModerationTask(job);

        //adding transcript task to job.
        if (uploadResult.GenerateVTT)
        {
            ConfigureTranscriptTask(job);
        }

        Stopwatch timer = new Stopwatch();
        timer.Start();
        //submit and execute job.
        job.Submit();
        job.GetExecutionProgressTask(new CancellationTokenSource().Token).Wait();
        timer.Stop();
        using (var sw = new StreamWriter(AmsConfigurations.logFilePath, true))
        {
            sw.WriteLine("AMS Job Elapsed Time: {0}", timer.Elapsed);
        }

        if (job.State == JobState.Error)
        {
            throw new Exception("Video moderation has failed due to AMS Job error.");
        }

        UploadAssetResult result = uploadResult;
        encodedAsset = job.OutputMediaAssets[0];
        result.ModeratedJson = GetCmDetail(job.OutputMediaAssets[1]);
        // Check for valid Moderated JSON
        var jsonModerateObject = JsonConvert.DeserializeObject<VideoModerationResult>(result.ModeratedJson);

        if (jsonModerateObject == null)
        {
            return false;
        }
        if (uploadResult.GenerateVTT)
        {
            GenerateTranscript(job.OutputMediaAssets.Last());
        }

        uploadResult.StreamingUrlDetails = PublishAsset(encodedAsset);
        string downloadUrl = GenerateDownloadUrl(asset, uploadVideoRequest.VideoName);
        uploadResult.StreamingUrlDetails.DownloadUri = downloadUrl;
        uploadResult.VideoName = uploadVideoRequest.VideoName;
        uploadResult.VideoFilePath = uploadVideoRequest.VideoFilePath;
        return true;
    }

A kód a következő feladatokat hajtja végre:

- Létrehoz egy AMS-feladatot a feldolgozáshoz
- Hozzáadja a videofájl kódolásához, moderálásához és szöveges átiratának elkészítéséhez szükséges feladatokat
- Elküldi a feladatot, feltölti a fájlt és megkezdi a feldolgozást
- Lekéri a moderálás eredményeit, a szöveges átiratot (amennyiben a felhasználó kérte), és egyéb adatokat

## <a name="sample-video-moderation-response"></a>Videomoderálási válasz – minta

A videomoderálási feladat eredménye (lásd: [videomoderálás gyors útmutatója](video-moderation-api.md)) egy JSON-adatstruktúra, amely a moderálás eredményeit tartalmazza. Az eredmények részletezik a videó töredékeit (jeleneteit), amelyek kulcskockákból álló, felülvizsgálatra megjelölt eseményeket (klipeket) tartalmaznak. A kulcskockák aszerint vannak pontozva, hogy milyen valószínűséggel tartalmaznak felnőtt vagy kényes tartalmat. A következő példa egy JSON-válasz:

    {
        "version": 2,
        "timescale": 90000,
        "offset": 0,
        "framerate": 50,
        "width": 1280,
        "height": 720,
        "totalDuration": 18696321,
        "fragments": [
        {
            "start": 0,
            "duration": 18000
        },
        {
            "start": 18000,
            "duration": 3600,
            "interval": 3600,
            "events": [
            [
            {
                "reviewRecommended": false,
                "adultScore": 0.00001,
                "racyScore": 0.03077,
                "index": 5,
                "timestamp": 18000,
                "shotIndex": 0
            }
            ]
        ]
        },
        {
            "start": 18386372,
            "duration": 119149,
            "interval": 119149,
            "events": [
            [
            {
                "reviewRecommended": true,
                "adultScore": 0.00000,
                "racyScore": 0.91902,
                "index": 5085,
                "timestamp": 18386372,
                "shotIndex": 62
            }
        ]
        ]
        }
    ]
    }

A `GenerateVTT` jelölő használatakor hangátirat is készül.

> [!NOTE]
> A konzolalkalmazás az [Azure Media Indexer API-val](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) átiratokat készít a feltöltött videó hangsávjából. Az eredmények WebVTT formátumban készülnek el. További információt erről a formátumról a [webes videók szövegsávos formátumáról](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API) szóló cikkben találhat.


## <a name="creating-the-human-in-the-loop-review"></a>Emberi tényezős értékelés létrehozása

A moderálási folyamat visszaadja a videó kulcskockáinak listáját, valamint a hangsávok átiratát. A következő lépés egy emberi moderátoroknak szóló értékelés elkészítése a Content Moderator felülvizsgálati eszközben. Ha visszatér a `Program.cs` `ProcessVideo()` metódusához, láthatja a `CreateVideoReviewInContentModerator()` metódus meghívását. Ez a metódus a `videoReviewApi` osztályba tartozik, amely a `VideoReviewAPI.cs` fájlban található, és itt látható.

    public async Task<string> CreateVideoReviewInContentModerator(UploadAssetResult uploadAssetResult)
    {
    
        string reviewId = string.Empty;
        List<ProcessedFrameDetails> frameEntityList = framegenerator.CreateVideoFrames(uploadAssetResult);
        string path = uploadAssetResult.GenerateVTT == true ? this._amsConfig.FfmpegFramesOutputPath + Path.GetFileNameWithoutExtension (uploadAssetResult.VideoName) + "_aud_SpReco.vtt" : "";
        TranscriptScreenTextResult screenTextResult = new TranscriptScreenTextResult();
        
    if (File.Exists(path))
        {
            screenTextResult = await GenerateTextScreenProfanity(reviewId, path, frameEntityList);
            uploadAssetResult.Category1TextScore = screenTextResult.Category1Score;
            uploadAssetResult.Category2TextScore = screenTextResult.Category2Score;
            uploadAssetResult.Category3TextScore = screenTextResult.Category3Score;
            uploadAssetResult.Category1TextTag = screenTextResult.Category1Tag;
            uploadAssetResult.Category2TextTag = screenTextResult.Category2Tag;
            uploadAssetResult.Category3TextTag = screenTextResult.Category3Tag;
        }
        
        var reviewVideoRequestJson = CreateReviewRequestObject(uploadAssetResult, frameEntityList);
        if (string.IsNullOrWhiteSpace(reviewVideoRequestJson))
        {
            throw new Exception("Video review process failed in CreateVideoReviewInContentModerator");
        }
        
        reviewId = JsonConvert.DeserializeObject<List<string>>(ExecuteCreateReviewApi(reviewVideoRequestJson).Result).FirstOrDefault();
        frameEntityList = framegenerator.GenerateFrameImages(frameEntityList, uploadAssetResult, reviewId);
        await CreateAndPublishReviewInContentModerator(uploadAssetResult, frameEntityList, reviewId, path, screenTextResult);
        return reviewId;
    
    }

A `CreateVideoReviewInContentModerator()` számos egyéb metódust meghív a következő feladatok elvégzéséhez:

> [!NOTE]
> A konzolalkalmazás az [FFmpeg](https://ffmpeg.org/) könyvtárat használja a miniatűrök létrehozásához. Ezek a miniatűrök (képek) a [videomoderálási kimenet](#sample-video-moderation-response) képkockáihoz tartozó időbélyegeknek felelnek meg.

|Tevékenység|Metódusok|Fájl|
|-|-|-|
|Kinyeri a kulcskockákat a videóból, és miniatűr képeket hoz létre róluk|`CreateVideoFrames()`<br>`GenerateFrameImages()`|`FrameGeneratorServices.cs`|
|Átvizsgálja a szöveges átiratot (amennyiben elérhető), és felnőtt vagy kényes hangtartalmat keres|`GenerateTextScreenProfanity()`| `VideoReviewAPI.cs`|
|Előkészít és beküld egy videoértékelést emberi vizsgálatra|`CreateReviewRequestObject()`<br> `ExecuteCreateReviewApi()`<br>`CreateAndPublishReviewInContentModerator()`|`VideoReviewAPI.cs`|

## <a name="video-review-default-view"></a>Videoértékelés – alapértelmezett nézet

A következő kép egy az előző lépések eredményeit mutatja.

![Videoértékelés – alapértelmezett nézet](images/video-tutorial-default-view.PNG)

## <a name="transcript-generation"></a>Átiratkészítés

Az oktatóanyagban használt kódok eddig a vizuális tartalmakhoz tartoztak. A beszédtartalom értékelése egy különálló (de nem kötelező) folyamat, amely – ahogyan korábban említettük – a hangsávból kinyert átiratot alkalmazza. Ideje megvizsgálnunk a szöveges átiratok készítési folyamatát, valamint azok felhasználását a felülvizsgálat során. Az átiratok készítése az [Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-index-content) szolgáltatás szerepköre.

Az alkalmazás a következő feladatokat hajtja végre:

|Tevékenység|Metódusok|Fájl|
|-|-|-|
|Meghatározza, hogy szükséges-e szöveges átiratok készítése|`Main()`<br>`GetUserInputs()`|`Program.cs`|
|Ha igen, hozzáad egy átírás feladatot a moderálás részeként|`ConfigureTranscriptTask()`|`VideoModerator.cs`|
|Lekéri az átirat helyi példányát|`GenerateTranscript()`|`VideoModerator.cs`|
|Megjelöli a kifogásolható hangot tartalmazó képkockákat|`GenerateTextScreenProfanity()`<br>`TextScreen()`|`VideoReviewAPI.cs`|
|Az eredményeket hozzáadja az értékeléshez|`UploadScreenTextResult()`<br>`ExecuteAddTranscriptSupportFile()`|`VideoReviewAPI.cs`|

### <a name="task-configuration"></a>Feladatkonfiguráció

Kezdjük azonnal az átírási feladat hozzáadásával. A már ismertetett `CreateAzureMediaServicesJobToModerateVideo()` meghívja a `ConfigureTranscriptTask()` parancsot.

    private void ConfigureTranscriptTask(IJob job)
    {
        string mediaProcessorName = _amsConfigurations.MediaIndexer2MediaProcessor;
        IMediaProcessor processor = _mediaContext.MediaProcessors.GetLatestMediaProcessorByName(mediaProcessorName);

        string configuration = File.ReadAllText(_amsConfigurations.MediaIndexerConfigurationJson);
        ITask task = job.Tasks.AddNew("AudioIndexing Task", processor, configuration, TaskOptions.None);
        task.InputAssets.Add(asset);
        task.OutputAssets.AddNew("AudioIndexing Output Asset", AssetCreationOptions.None);
    }

A program az átírási feladat konfigurációját a `Lib` mappa `MediaIndexerConfig.json` fájljából olvassa be. Létrejönnek a konfigurációs fájl és az átírási folyamat kimenetének AMS-eszközei. Az AMS-feladat futása alatt a feladat létrehoz egy szöveges átiratot a videofájl hangsávjából.

> [!NOTE]
> A mintaalkalmazás csak az amerikai angol nyelvű szöveget ismeri fel.

### <a name="transcript-generation"></a>Átiratkészítés

Az átirat AMS-eszközként lesz közzétéve. Az átirat kifogásolható tartalmak utáni vizsgálatához az alkalmazás letölti az eszközt az Azure Media Services szolgáltatásból. A `CreateAzureMediaServicesJobToModerateVideo()` meghívja az itt látható `GenerateTranscript()` parancsot, hogy lekérje a fájlt.

    public bool GenerateTranscript(IAsset asset)
    {
        try
        {
            var outputFolder = this._amsConfigurations.FfmpegFramesOutputPath;
            IAsset outputAsset = asset;
            IAccessPolicy policy = null;
            ILocator locator = null;
            policy = _mediaContext.AccessPolicies.Create("My 30 days readonly policy", TimeSpan.FromDays(360), AccessPermissions.Read);
            locator = _mediaContext.Locators.CreateLocator(LocatorType.Sas, outputAsset, policy, DateTime.UtcNow.AddMinutes(-5));
            DownloadAssetToLocal(outputAsset, outputFolder);
            locator.Delete();
            return true;
        }
        catch
        {   //TODO:  Logging
            Console.WriteLine("Exception occurred while generating index for video.");
            throw;
        }
    }

Az AMS szükséges beállítása után a letöltést a `DownloadAssetToLocal()` parancs hívásával indíthatja el, amely egy általános, AMS-eszközöket egy helyi fájlba másoló függvény.

## <a name="transcript-moderation"></a>Átiratmoderálás

A kész átiratokat beolvasva felhasználhatja őket az értékeléshez. Az értékelés létrehozása a `CreateVideoReviewInContentModerator()` parancs feladata, amely a `GenerateTextScreenProfanity()` parancsot hívja meg a feladathoz. Ez a metódus ezután a `TextScreen()` parancsot hívja meg, amely a legtöbb funkciót tartalmazza. 

A `TextScreen()` a következő feladatokat hajtja végre:

- Az átirat elemzése időbélyegek és feliratok kereséséhez
- Feliratok beküldése szövegmoderálásra
- Potenciálisan kifogásolható beszédtartalmat tartalmazó képkockák megjelölése

Vizsgáljuk meg részletesebben ezeket a feladatokat:

### <a name="initialize-the-code"></a>A kód inicializálása

Első lépésként inicializálja az összes változót és gyűjteményt.

    private async Task<TranscriptScreenTextResult> TextScreen(string filepath, List<ProcessedFrameDetails> frameEntityList)
    {
        List<TranscriptProfanity> profanityList = new List<TranscriptProfanity>();
        string responseContent = string.Empty;
        HttpResponseMessage response;
        bool category1Tag = false;
        bool category2Tag = false;
        bool category3Tag = false;
        double category1Score = 0;
        double category2Score = 0;
        double category3Score = 0;
        List<string> vttLines = File.ReadAllLines(filepath).Where(line => !line.Contains("NOTE Confidence:") && line.Length > 0).ToList();
        StringBuilder sb = new StringBuilder();
        List<CaptionScreentextResult> csrList = new List<CaptionScreentextResult>();
        CaptionScreentextResult captionScreentextResult = new CaptionScreentextResult() { Captions = new List<string>() };

        // Code from the next sections in the tutorial
    

### <a name="parse-the-transcript-for-captions"></a>Az átirat elemzése feliratok kereséséhez

Következő lépésként elemezze a VTT formátumú átiratot feliratok és időbélyegek kereséséhez. A felülvizsgálati eszköz ezeket a feliratokat a videoértékelési képernyő Átirat lapján jeleníti meg. Az időbélyegek a feliratok a megfelelő képkockákkal való szinkronizálását szolgálják.

        // Code from the previous section(s) in the tutorial

        //
        // Parse the transcript
        //
        foreach (var line in vttLines.Skip(1))
        {
                if (line.Contains("-->"))
                {
                    if (sb.Length > 0)
                    {
                        captionScreentextResult.Captions.Add(sb.ToString());
                        sb.Clear();
                    }
                    if (captionScreentextResult.Captions.Count > 0)
                    {
                        csrList.Add(captionScreentextResult);
                        captionScreentextResult = new CaptionScreentextResult() { Captions = new List<string>() };
                    }
                    string[] times = line.Split(new string[] { "-->" }, StringSplitOptions.RemoveEmptyEntries);
                    string startTimeString = times[0].Trim();
                    string endTimeString = times[1].Trim();
                    int startTime = (int)TimeSpan.ParseExact(startTimeString, @"hh\:mm\:ss\.fff", CultureInfo.InvariantCulture).TotalMilliseconds;
                    int endTime = (int)TimeSpan.ParseExact(endTimeString, @"hh\:mm\:ss\.fff", CultureInfo.InvariantCulture).TotalMilliseconds;
                    captionScreentextResult.StartTime = startTime;
                    captionScreentextResult.EndTime = endTime;
                }
                else
                {
                    sb.Append(line);
                }
                if (sb.Length + line.Length > 1024)
                {
                    captionScreentextResult.Captions.Add(sb.ToString());
                    sb.Clear();
                }
            }
            if (sb.Length > 0)
            {
                captionScreentextResult.Captions.Add(sb.ToString());
            }
            if (captionScreentextResult.Captions.Count > 0)
            {
                csrList.Add(captionScreentextResult);
            }

            // Code from the following section in the quickstart

### <a name="moderate-captions-with-the-text-moderation-service"></a>Felirat moderálása a szövegmoderálási szolgáltatással

Következő lépésként beolvassuk az elemzett szöveges feliratokat a Content Moderator szöveges API-jával.

> [!NOTE]
> A Content Moderator szolgáltatáskulcs egy másodpercenkénti kérelmekre (RPS-re) vonatkozó korláttal rendelkezik. Ha túllépi ezt a korlátot, az SDK 429-es hibakódú kivételt jelez. 
>
> Az ingyenes szint kulcsai egy RPS-korláttal bírnak.

    //
    // Moderate the captions or cues
    //
    int waitTime = 1000;
    foreach (var csr in csrList)
    {
                bool captionAdultTextTag = false;
                bool captionRacyTextTag = false;
                bool captionOffensiveTextTag = false;
                bool retry = true;

                foreach (var caption in csr.Captions)
                {
                    while (retry)
                    {
                        try
                        {
                            System.Threading.Thread.Sleep(waitTime);
                            var lang = await CMClient.TextModeration.DetectLanguageAsync("text/plain", caption);
                            var oRes = await CMClient.TextModeration.ScreenTextWithHttpMessagesAsync(lang.DetectedLanguageProperty, "text/plain", caption, null, null, null, true);
                            response = oRes.Response;
                            responseContent = await response.Content.ReadAsStringAsync();
                            retry = false;
                        }
                        catch (Exception e)
                        {
                            if (e.Message.Contains("429"))
                            {
                                Console.WriteLine($"Moderation API call failed. Message: {e.Message}");
                                waitTime = (int)(waitTime * 1.5);
                                Console.WriteLine($"wait time: {waitTime}");
                            }
                            else
                            {
                                retry = false;
                                Console.WriteLine($"Moderation API call failed. Message: {e.Message}");
                            }
                        }
                    }
                    var jsonTextScreen = JsonConvert.DeserializeObject<TextScreen>(responseContent);
                    if (jsonTextScreen != null)
                    {
                        TranscriptProfanity transcriptProfanity = new TranscriptProfanity();
                        transcriptProfanity.TimeStamp = "";
                        List<Terms> transcriptTerm = new List<Terms>();
                        if (jsonTextScreen.Terms != null)
                        {
                            foreach (var term in jsonTextScreen.Terms)
                            {
                                var profanityobject = new Terms
                                {
                                    Term = term.Term,
                                    Index = term.Index
                                };
                                transcriptTerm.Add(profanityobject);
                            }
                            transcriptProfanity.Terms = transcriptTerm;
                            profanityList.Add(transcriptProfanity);
                        }
                        if (jsonTextScreen.Classification.Category1.Score > _amsConfig.Category1TextThreshold) captionAdultTextTag = true;
                        if (jsonTextScreen.Classification.Category2.Score > _amsConfig.Category2TextThreshold) captionRacyTextTag = true;
                        if (jsonTextScreen.Classification.Category3.Score > _amsConfig.Category3TextThreshold) captionOffensiveTextTag = true;
                        if (jsonTextScreen.Classification.Category1.Score > _amsConfig.Category1TextThreshold) category1Tag = true;
                        if (jsonTextScreen.Classification.Category2.Score > _amsConfig.Category2TextThreshold) category2Tag = true;
                        if (jsonTextScreen.Classification.Category3.Score > _amsConfig.Category3TextThreshold) category3Tag = true;
                        category1Score = jsonTextScreen.Classification.Category1.Score > category1Score ? jsonTextScreen.Classification.Category1.Score : category1Score;
                        category2Score = jsonTextScreen.Classification.Category2.Score > category2Score ? jsonTextScreen.Classification.Category2.Score : category2Score;
                        category3Score = jsonTextScreen.Classification.Category3.Score > category3Score ? jsonTextScreen.Classification.Category3.Score : category3Score;
                    }
                    foreach (var frame in frameEntityList.Where(x => x.TimeStamp >= csr.StartTime && x.TimeStamp <= csr.EndTime))
                    {
                        frame.IsAdultTextContent = captionAdultTextTag;
                        frame.IsRacyTextContent = captionRacyTextTag;
                        frame.IsOffensiveTextContent = captionOffensiveTextTag;
                    }
                }
            }
            TranscriptScreenTextResult screenTextResult = new TranscriptScreenTextResult()
            {
                TranscriptProfanity = profanityList,
                Category1Tag = category1Tag,
                Category2Tag = category2Tag,
                Category3Tag = category3Tag,
                Category1Score = category1Score,
                Category2Score = category2Score,
                Category3Score = category3Score
            };
            return screenTextResult;
    }

### <a name="breaking-down-the-text-moderation-step"></a>A szövegmoderálás részletes ismertetése

A `TextScreen()` egy összetett metódus, ezért részletesen ismertetjük.

1. A metódus először soronként elolvassa az átiratot. Az üres sorokat és megbízhatósági pontszámmal rendelkező `NOTE`-elemeket figyelmen kívül hagyja. Kinyeri az időbélyegzőket és szöveges elemeket a fájl *jeleiből*. A jelek a hangsáv szövegét képviselik, és kezdési és befejezési idővel rendelkeznek. A jelek a(z) `-->` sztringet tartalmazó időbélyegvonallal kezdődnek. Ezt egy vagy több sornyi szöveg követi.

1. A (`TranscriptProfanity.cs` elemben meghatározott) `CaptionScreentextResult` példányai tárolják az egyes jelek elemzett adatait.  Új időbélyegsor észlelésekor vagy a maximális szöveghossz (1024 karakter) elérésekor a program új `CaptionScreentextResult` elemet ad hozzá a következőhöz: `csrList`. 

1. A metódus ezután elküldi a jeleket a Text Moderation API-nak. Meghívja a `ContentModeratorClient.TextModeration.DetectLanguageAsync()` és a `ContentModeratorClient.TextModeration.ScreenTextWithHttpMessagesAsync()` függvényeket, amelyek a `Microsoft.Azure.CognitiveServices.ContentModerator` szerelvényben vannak definiálva. A sebességkorlátozás elkerülése érdekében a metódus minden jel beküldése előtt egy másodperc szünetet tart.

1. Miután megkapja a Text Moderation szolgáltatás eredményeit, a metódus elemzi azokat, és megállapítja, hogy megfelelnek-e a megbízhatósági küszöbértékeknek. Ezek az értékek az `App.config` fájlban, a következőkként vannak meghatározva: `OffensiveTextThreshold`, `RacyTextThreshold` és `AdultTextThreshold`. A szolgáltatás végül tárolja magukat a kifogásolható kifejezéseket is. A jel időtartományán belül minden képkockát sértő, kényes és/vagy felnőtt szövegként jelöl meg.

1. A `TextScreen()` egy `TranscriptScreenTextResult` példányt eredményez, amely a teljes videó szövegmoderálásának eredményét tartalmazza. Ez az objektum a különböző típusú kifogásolható tartalmak jelölőit és pontszámait tartalmazza, valamint az összes kifogásolható kifejezést. A hívó (`CreateVideoReviewInContentModerator()`) meghívja az `UploadScreenTextResult()` parancsot a fenti adatok az értékeléshez való csatolásához, hogy azok az emberi felülvizsgálat során is elérhetők legyenek.
 
## <a name="video-review-transcript-view"></a>Videoértékelés – átiratnézet

A következő kép az átiratkészítés és a moderálási lépések eredményét jeleníti meg.

![Videomoderálás – átiratnézet](images/video-tutorial-transcript-view.PNG)

## <a name="program-output"></a>A program kimenete

A program következő parancssori kimenete a különböző végrehajtott feladatokat mutatja. Emellett a moderálás (JSON formátumú) eredménye és a beszédátirat is elérhető ugyanabban a könyvtárban, ahol az eredeti videofájlok.

    Microsoft.ContentModerator.AMSComponentClient
    Enter the fully qualified local path for Uploading the video :
    "Your File Name.MP4"
    Generate Video Transcript? [y/n] : y
    
    Video compression process started...
    Video compression process completed...
    
    Video moderation process started...
    Video moderation process completed...
    
    Video review process started...
    Video Frames Creation inprogress...
    Frames(83) created successfully.
    Review Created Successfully and the review Id 201801va8ec2108d6e043229ba7a9e6373edec5
    Video review successfully completed...
    
    Total Elapsed Time: 00:05:56.8420355


## <a name="next-steps"></a>További lépések

[Töltse le a Visual Studio szolgáltatást](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) és az oktatóanyaghoz szükséges a mintafájlokat és könyvtárakat, és tegye meg az integráció első lépéseit.
