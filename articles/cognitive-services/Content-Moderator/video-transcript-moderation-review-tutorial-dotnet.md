---
title: Az Azure Content Moderator – mérsékelt videók és a .NET-ben szövegekben |} A Microsoft Docs
description: Hogyan használható Content Moderator közepes videók és a .NET-ben szövegekben.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 1/27/2018
ms.author: sajagtap
ms.openlocfilehash: 0f851c030a05880d79a998ed4b4a941082c057b9
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37865471"
---
# <a name="video-and-transcript-moderation-tutorial"></a>A videó és a szöveges moderálás oktatóanyag

A Content Moderator video API-k lehetővé teszik a közepes szintű videókat, és hozzon létre videót felülvizsgálatok az emberi ellenőrző eszközökkel. 

Ez a részletes oktatóanyag segít megérteni, hogyan hozhat létre egy teljes videó és a szöveges moderálás megoldás a gépi támogatású képmoderálás és emberi hurok felülvizsgálat létrehozása.

Töltse le a [C# konzolalkalmazást](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) ehhez az oktatóanyaghoz. A konzolalkalmazást az SDK és a kapcsolódó csomagok használja a következő feladatokat:

- A gyorsabb feldolgozása a bemeneti video(s) tömörítése
- A videó helyességének és elemzésekkel keretek mérsékelt
- Keret időbélyegei használatával miniatűrképet (képek) létrehozása
- Küldje el az időbélyegeket és a miniatűrök videót felülvizsgálatok létrehozása
- Szöveggé a videó (szöveges) a Media Indexer API-val
- Üzletmenetre gyakorolt közepes az átiratok a szöveg moderálása szolgáltatással
- A moderált átiratok a videó felülvizsgálat hozzáadása

## <a name="sample-program-outputs"></a>Minta program kimenete

Mielőtt továbblép, a program a feloldását kérte minta kimenetek vizsgáljuk meg:

- [Konzolkimenet](#program-output)
- [Videós áttekintése](#video-review-default-view)
- [Átirat megjelenítése](#video-review-transcript-view)

## <a name="prerequisites"></a>Előfeltételek

1. Regisztráljon a [Content Moderator felülvizsgálati eszközben](https://contentmoderator.cognitive.microsoft.com/) webhely és [hozzon létre egyéni címkék](Review-Tool-User-Guide/tags.md) , amely a kód rendeli hozzá a C# konzolalkalmazást. A következő képernyőn látható az egyéni címkéket.

  ![Egyéni címkék videomoderálás](images/video-tutorial-custom-tags.png)

1. A mintaalkalmazás futtatása az Azure-fiók és az Azure Media Services-fiók szükséges. Ezenkívül a Content Moderator privát előzetes verzió való hozzáférésre van szüksége. Végül kell az Azure Active Directory hitelesítő adatok. Ezek az információk beszerzése a részletekért lásd: [a videó moderálási API – rövid útmutató](video-moderation-api.md).

1. Szerkessze a fájlt `App.config` és az Active Directory-bérlő nevét, a Szolgáltatásvégpontok hozzáadása és előfizetési kulcsok által jelzett `#####`. A következő információk szükségesek:

|Kulcs|Leírás|
|-|-|
|`AzureMediaServiceRestApiEndpoint`|Az Azure Media Services (AMS) API-végpontokat|
|`ClientSecret`|Az Azure Media Services előfizetési kulcs|
|`ClientId`|Az Azure Media Services ügyfél-azonosító|
|`AzureAdTenantName`|Active Directory-szervezet jelölő bérlő neve|
|`ContentModeratorReviewApiSubscriptionKey`|Az előfizetői a Content Moderator API áttekintése|
|`ContentModeratorApiEndpoint`|A Content Moderator API-végpontokat|
|`ContentModeratorTeamId`|A Content moderator csoport azonosítója|

## <a name="getting-started"></a>Első lépések

Az osztály `Program` a `Program.cs` a fő belépési pont a videomoderálás alkalmazáshoz.

### <a name="methods-of-class-program"></a>Program osztály metódusain

|Módszer|Leírás|
|-|-|
|`Main`|Parancssor elemzi, gyűjti össze a felhasználói bevitel, és elindítja a feldolgozása.|
|`ProcessVideo`|Tömöríti, feltölti, módosítja, és létrehozza a videót felülvizsgálatok.|
|`CreateVideoStreamingRequest`|Létrehoz egy stream szolgáltatást videó feltöltésére|
|`GetUserInputs`|Gyűjti össze a felhasználói bevitel; amikor nincs parancssori kapcsolók találhatók.|
|`Initialize`|Inicializálja a moderálási folyamat szükséges objektumok|

### <a name="the-main-method"></a>A Main metódushoz

`Main()` a helyzet ahol végrehajtási elindul, a hely, ahol ismertetése a videomoderálás folyamat elindításához.

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

`Main()` kezeli a következő parancssori argumentumok:

- MPEG-4 videofájlok el a moderálás tartalmazó könyvtár elérési útja. Az összes `*.mp4` moderálás küldenek el, illetve annak alkönyvtáraiba ebben a könyvtárban lévő fájlok.
- Ha szeretné logikai (igaz/hamis) jelző. e szöveges szövegekben céljából moderálási hang hozható létre.

Ha nincs parancssori argumentumokra jelen, `Main()` hívások `GetUserInputs()`. Ez a módszer kéri a felhasználót, adja meg az elérési útját egy videó fájlt, és adja meg, hogy egy szöveges átiratok elő kell állítani.

> [!NOTE]
> A Konzolalkalmazás használja a [az Azure Media Indexer API](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) szövegekben generálhatnak a feltöltött videókhoz hangsávra. Az eredmények WebVTT formátumban vannak megadva. Ezt a formátumot a további információkért lásd: [webes videó nyomon követi szövegformátum](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API).

### <a name="initialize-and-processvideo-methods"></a>Inicializálás és ProcessVideo módszerek

Függetlenül attól, hogy a program beállításai származnak, interaktív felhasználói bevitelt, vagy a parancssorból `Main()` tovább hívások `Initialize()` hozhat létre a következő esetekben:

|Osztály|Leírás|
|-|-|
|`AMSComponent`|Videofájlok tömöríti azokat a moderálás elküldése előtt.|
|`AMSconfigurations`|Felületet talált az alkalmazás konfigurációs adatok `App.config`.|
|`VideoModerator`| Feltöltése, a kódolási, titkosítási és a moderálás AMS SDK-val|
|`VideoReviewApi`|A Content Moderator szolgáltatásban videót felülvizsgálatok kezeli|

Ezeket az osztályokat (aside a `AMSConfigurations`, amely nagyon egyszerű) terjed ki a ennek az oktatóanyagnak a későbbi szakaszokban részletesebben.

Végül, a videó fájlok meghívásával feldolgozott egyszerre csak egy `ProcessVideo()` minden.

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


A `ProcessVideo()` módszer nagyon egyszerű. A következő műveleteket a sorrendben hajtja végre:

- Tömöríti a videót
- A videó feltölti az Azure Media Services-eszköz
- Létrehoz egy AMS feladatot a videó mérsékelt
- Létrehoz egy videó tekintse át a Content Moderator

A következő szakaszok fontolja meg részletesen az egyes folyamatok által meghívott némelyike `ProcessVideo()`. 

## <a name="compressing-the-video"></a>A videó tömörítése

Hálózati forgalom minimalizálása érdekében az alkalmazás videofájlok H.264 (AVC MPEG-4-formátum) formátumra alakítja át, és a maximális 640 képpont szélességű méretezhető őket. A H.264 kodek ajánlott a nagy hatékonyságú (tömörítési sebesség) miatt. A tömörítés történik az ingyenes használatával `ffmpeg` parancssori eszköz, amely tartalmazza a `Lib` mappát a Visual Studio-megoldás. A bemeneti fájlok bármely által támogatott formátum lehet `ffmpeg`, beleértve a leggyakrabban használt videofájl formátumai és kodekei.

> [!NOTE]
> Amikor a program parancssori kapcsolókkal, meg kell adnia egy el a moderálás videó fájlokat tartalmazó mappát. Minden fájl a könyvtárban kellene a `.mp4` fájlnév-kiterjesztés dolgozza fel. Egyéb fájlnévkiterjesztések feldolgozásához, frissítse a `Main()` metódus az `Program.cs` tartalmazza a kívánt bővítményeket.

A kód, amely egyetlen videofájl tömöríti a `AmsComponent` osztály `AMSComponent.cs`. Ez a szolgáltatás felelős a módszer `CompressVideo()`, itt látható.

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

- Ellenőrzi, hogy ellenőrizze, hogy a konfigurációt `App.config` minden szükséges adatot tartalmaz
- Ellenőrzi, győződjön meg arról, hogy a `ffmpeg` bináris jelen
- Létrehozza a kimeneti fájl nevét hozzáfűzésével `_c.mp4` alap a fájl nevét (például `Example.mp4`  ->  `E>xample_c.mp4`)
- Összeállítja az átalakítás végrehajtásához parancssori karakterlánccal
- Elindítja egy `ffmpeg` feldolgozni a parancssor használatával
- A videó feldolgozásra vár

> [!NOTE]
> Ha ismeri a videók már tömörített H.264 használatával, és rendelkezik a megfelelő méretben, újraírási `CompressVideo()` kihagyja a tömörítést.

A metódus a tömörített kimeneti fájl nevét adja vissza.

## <a name="uploading-and-moderating-the-video"></a>Fel- és a videó moderálása

A videó előtt fel lehessen dolgozni a tartalom-jóváhagyás szolgáltatás által az Azure Media Servicesben kell tárolni. A `Program` osztály `Program.cs` rövid metódus `CreateVideoStreamingRequest()` , amely a videó feltöltése használt streamelési kérés képviselő objektumot adja vissza.

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

A létrejövő `UploadVideoStreamRequest` objektumhoz definiált `UploadVideoStreamRequest.cs` (és a fölérendelt `UploadVideoRequest`, a `UploadVideoRequest.cs`). Ezek az osztályok nem jelennek meg. ezek rövid és a csak a videó a tömörített adatok és információk tárolására szolgál. Egy másik csak adatokat osztály `UploadAssetResult` (`UploadAssetResult.cs`) a feltöltési folyamat eredményeinek tárolására szolgál. Most már lehet tudni, hogy ezeket a sorokat `ProcessVideo()`:

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

Ezek a sorok hajtsa végre a következő feladatokat:

- Hozzon létre egy `UploadVideoStreamRequest` a tömörített videó feltöltése
- Állítsa be a kérés `GenerateVTT` jelzőt, ha a felhasználó által kért egy szöveget szöveges
- Hívások `CreateAzureMediaServicesJobToModerateVideo()` az eredményt kap, és hajtsa végre a feltöltés

## <a name="deep-dive-into-video-moderation"></a>Videomoderálás részletes ismertetése

A metódus `CreateAzureMediaServicesJobToModerateVideo()` van `VideoModerator.cs`, amely tartalmazza a kódot, amely együttműködik az Azure Media Services a nagy. A metódus forráskódját a következő kivonat jelenik meg.

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

Ez a kód a következő feladatokat hajtja végre:

- El kell végezni a feldolgozást egy AMS feladatot hoz létre
- Hozzáadja a fájl kódolása, moderálása, és a egy szöveget szöveges generálása feladatok
- Elküldi a feladatot a fájl- és kezdő feldolgozási feltöltése
- Lekéri a moderálás eredmények, a szöveg a szövegben (ha szükséges) és egyéb információk

## <a name="sample-video-moderation-response"></a>Videomoderálás mintaválasz

A videomoderálás feladat eredménye (lásd: [videomoderálás rövid](video-moderation-api.md) a moderálás eredményeit tartalmazó JSON-adatstruktúra. Ezeket az eredményeket a szilánkok (helyességének) tartalmazza a videót, minden egyes tartalmazó eseményeket (klipek), amely rendelkezik lett-e jelölve, tekintse át a főbb keretek belül. Minden egyes kulcs keret valószínűsége szerint sorolódik úgy, hogy felnőtt vagy pikáns tartalmak tartalmazza. A következő példában egy JSON-választ:

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

Egy, a Videó hangjának beszédátírási egyben állítja elő, amikor a `GenerateVTT` jelző be van állítva.

> [!NOTE]
> A Konzolalkalmazás használja a [az Azure Media Indexer API](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) szövegekben generálhatnak a feltöltött videókhoz hangsávra. Az eredmények WebVTT formátumban vannak megadva. Ezt a formátumot a további információkért lásd: [webes videó nyomon követi szövegformátum](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API).


## <a name="creating-the-human-in-the-loop-review"></a>Az emberi hurok felülvizsgálat létrehozása

A moderálási folyamat a videót, és a egy saját hangsáv, átirat kulcs keretek listáját adja vissza. A következő lépés, hogy létrehoz egy felülvizsgálatot a Content Moderator felülvizsgálati eszközben az emberi moderátorok. Visszatérve a `ProcessVideo()` metódus az `Program.cs`, láthatja a hívást a `CreateVideoReviewInContentModerator()` metódust. Ez a módszer van a `videoReviewApi` osztály, amely a `VideoReviewAPI.cs`, és itt jelenik meg.

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

`CreateVideoReviewInContentModerator()` a következő feladatok végrehajtására számos más módszereket hív meg:

> [!NOTE]
> Használja a konzolalkalmazást a [FFmpeg](https://ffmpeg.org/) miniatűrök létrehozása a könyvtárában. Ezek a miniatűrök (képek) felel meg a keret időbélyegeket az a [videomoderálás kimeneti](#sample-video-moderation-response).

|Tevékenység|Metódusok|Fájl|
|-|-|-|
|A kulcs a videó a keretek, és azokat a miniatűr képeket hoz létre kivonatot|`CreateVideoFrames()`<br>`GenerateFrameImages()`|`FrameGeneratorServices.cs`|
|A szöveg, átirat vizsgálata, ha elérhető, keresse meg a felnőtt vagy pikáns hang|`GenerateTextScreenProfanity()`| `VideoReviewAPI.cs`|
|Előkészítése, és elküldi az emberi ellenőrző kérelem egy Videós áttekintése|`CreateReviewRequestObject()`<br> `ExecuteCreateReviewApi()`<br>`CreateAndPublishReviewInContentModerator()`|`VideoReviewAPI.cs`|

## <a name="video-review-default-view"></a>Alapértelmezett nézet Videós áttekintése

A következő képernyőn az előző lépések eredményeit jeleníti meg.

![Alapértelmezett nézet Videós áttekintése](images/video-tutorial-default-view.PNG)

## <a name="transcript-generation"></a>Átirat generálása

Eddig ebben az oktatóanyagban bemutatott a kódot a vizuális tartalmak rendelkezik összpontosít. Beszédtartalom át az elkülönített és nem kötelező, amely, ahogy már említettük, egy a hanganyag generált átiratok használja. Eljött az idő már, tekintse meg, hogyan szöveg szövegekben létrehozott és a felülvizsgálati folyamat használja. A feladat az átiratot létrehozni bloberőforrásokhoz hárul, a [Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-index-content) szolgáltatás.

Az alkalmazás a következő feladatokat hajtja végre:

|Tevékenység|Metódusok|Fájl|
|-|-|-|
|Van-e szöveges szövegekben jöjjön létre|`Main()`<br>`GetUserInputs()`|`Program.cs`|
|Ha igen, moderálási részeként beszédátírási feladat elküldése|`ConfigureTranscriptTask()`|`VideoModerator.cs`|
|Az átirat helyi példánya|`GenerateTranscript()`|`VideoModerator.cs`|
|Ez a jelző azt a videó a nem megfelelő hangot tartalmazó képkockák|`GenerateTextScreenProfanity()`<br>`TextScreen()`|`VideoReviewAPI.cs`|
|A felülvizsgálati eredmények hozzáadása|`UploadScreenTextResult()`<br>`ExecuteAddTranscriptSupportFile()`|`VideoReviewAPI.cs`|

### <a name="task-configuration"></a>A feladat konfigurációja

Jobb tekintse át a beszédátírási feladat elküldése. `CreateAzureMediaServicesJobToModerateVideo()` (a már leírtak szerint) hívásokat `ConfigureTranscriptTask()`.

    private void ConfigureTranscriptTask(IJob job)
    {
        string mediaProcessorName = _amsConfigurations.MediaIndexer2MediaProcessor;
        IMediaProcessor processor = _mediaContext.MediaProcessors.GetLatestMediaProcessorByName(mediaProcessorName);

        string configuration = File.ReadAllText(_amsConfigurations.MediaIndexerConfigurationJson);
        ITask task = job.Tasks.AddNew("AudioIndexing Task", processor, configuration, TaskOptions.None);
        task.InputAssets.Add(asset);
        task.OutputAssets.AddNew("AudioIndexing Output Asset", AssetCreationOptions.None);
    }

Az átirat feladat konfigurációját a fájlból olvasható `MediaIndexerConfig.json` a megoldásban `Lib` mappát. AMS-eszközök konfigurációs fájl és a kimenet a beszédátírási folyamat jön létre. Az AMS-feladat fut, amikor ez a feladat egy szöveget szöveges a videofájl hangsávot hoz létre.

> [!NOTE]
> A mintaalkalmazás csak az angol speech felismeri.

### <a name="transcript-generation"></a>Átirat generálása

Az átirat AMS eszközként van közzétéve. Az átirat nem kívánt tartalom vizsgálata, az alkalmazás az eszköz az Azure Media Services tölti le. `CreateAzureMediaServicesJobToModerateVideo()` hívások `GenerateTranscript()`, látható itt, hogy kérje le a fájlt.

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
            Console.WriteLine("Exception occured while generating index for video.");
            throw;
        }
    }

Néhány szükséges AMS a telepítés után a tényleges letöltése történik meghívásával `DownloadAssetToLocal()`, általános függvény, amely átmásol egy AMS-objektumot egy helyi fájlba.

## <a name="transcript-moderation"></a>Átirat moderálása

Az átirat kezelésére, a beolvasott és a felülvizsgálati használt. Az adatvesztéssel, a felülvizsgálat létrehozása `CreateVideoReviewInContentModerator()`, a hívások `GenerateTextScreenProfanity()` , ezt a feladatot. Ez a metódus meghívja `TextScreen()`, amely tartalmazza a legtöbb funkciója. 

`TextScreen()` a következő feladatokat hajtja végre:

- Az átirat alkalommal tamps és akadálymentes feliratok elemzése
- Küldje el az egyes szövegmoderálás felirata.
- Ez a jelző azt, amelyek esetlegesen nemkívánatos beszédtartalom keretet

Vizsgáljuk ezeket a feladatokat részletesen:

### <a name="initialize-the-code"></a>A kód inicializálása

Első lépésként inicializálni a változók és a gyűjtemények.

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
    

### <a name="parse-the-transcript-for-captions"></a>A feliratok átiratot elemzése

Következő lépésként elemezni a VTT formázott szöveges feliratot és időbélyegek. A felülvizsgálati eszköz ezeket a feliratokat az Átiratok lapon, a videó felülvizsgálati képernyőn jeleníti meg. A feliratok a megfelelő videókban való szinkronizálása időbélyegei szolgálnak.

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

### <a name="moderate-captions-with-the-text-moderation-service"></a>A szöveg moderálása szolgáltatással mérsékelt feliratok

Ezután tudjuk ellenőrizni az elemzett szöveg feliratok, a Content Moderator API szöveggel.

> [!NOTE]
> A Content Moderator Szolgáltatáskulcs rendelkezik egy második (RPS) sávszélesség-korlátjának kérelemből. Ha túllépi a korlátot, akkor az SDK-t egy 429 hibakód kivételt jelez. 
>
> Ingyenes szint kulcs esetében egy függő Entitás sebessége.

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

### <a name="breaking-down-the-text-moderation-step"></a>A szöveg moderálása lépés bontásához

`TextScreen()` a jelentős módszer, úgyhogy azt felosztania.

1. Először a metódus beolvassa a szöveges fájl soronként. Üres és tartalmazó sorokat figyelmen kívül hagyja a `NOTE` a rendszer egy magabiztossági pontszámot. Az időbélyegzőket és a szöveges elemek kibontja a *jelek* a fájlban. Köteg hangsávok származó szöveget jelöl, és tartalmazza a kezdési és befejezési idejének. A stamp idővonalát karakterlánccal kezdődik köteg `-->`. Egy vagy több sornyi szöveg követ.

1. A példányok `CaptionScreentextResult` (meghatározott `TranscriptProfanity.cs`) minden egyes köteg az elemzett adatok tárolására szolgálnak.  Ha egy új stamp idősorán észlel, illetve az 1024 karakter hosszú szöveg maximális hossza elérésekor, új `CaptionScreentextResult` adnak hozzá a `csrList`. 

1. A metódus ezután elküldi a moderálási API, minden egyes köteg. Meghívja mindkét `ContentModeratorClient.TextModeration.DetectLanguageAsync()` és `ContentModeratorClient.TextModeration.ScreenTextWithHttpMessagesAsync()`, amelyek meghatározott a `Microsoft.Azure.CognitiveServices.ContentModerator` sestavení. Folyamatban van a forgalom korlátozott elkerüléséhez a metódus egy második minden egyes köteg elküldése előtt felfüggeszti.

1. Miután kapott eredmények a Szövegmoderálás szolgáltatásból, a metódus ezután elemzi ezeket a megfelelnek-e megbízhatósági küszöbértékek megtekintéséhez. Ezeket az értékeket a rendszer létrehozott `App.config` , `OffensiveTextThreshold`, `RacyTextThreshold`, és `AdultTextThreshold`. Végül a nem kívánt feltételeket, maguk is tárolja. A köteg idő tartományon belüli összes keretet tartalmazó sértő, rasszista, illetve a felnőtt szöveg szerint vannak megjelölve.

1. `TextScreen()` Visszaadja egy `TranscriptScreenTextResult` példányt, amely tartalmazza a videó teljes szöveg moderálása eredménye. Ez az objektum jelzőket tartalmaz, és nem kívánt tartalmat, minden nemkívánatos feltételek mellett a különböző típusú pontszámmodell. A hívó `CreateVideoReviewInContentModerator()`, hívások `UploadScreenTextResult()` csatlakoztassa ezeket az információkat a felülvizsgálatot, hogy az emberi teszik a felülvizsgálók számára elérhető legyen.
 
## <a name="video-review-transcript-view"></a>Szöveges nézet Videós áttekintése

A következő képernyőt jeleníti meg a az átiratot és a moderálás lépéseket.

![Videomoderálás átirat megjelenítése](images/video-tutorial-transcript-view.PNG)

## <a name="program-output"></a>Program kimenete

A program a következő parancssori kimenet látható a különböző feladatok, módon végezhető el. Ezenkívül a moderálás eredmény (JSON formátumban), és a beszéd szöveges érhetők el az eredeti videó fájlok könyvtárába.

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

[Töltse le a Visual Studio-megoldás](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) példa a fájlok és a szükséges kódtárak ehhez az oktatóanyaghoz, és az integráció használatának első lépései.
