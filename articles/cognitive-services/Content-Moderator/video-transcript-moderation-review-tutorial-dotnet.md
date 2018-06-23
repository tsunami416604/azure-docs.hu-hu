---
title: Az Azure Content moderátor - mérsékelt videók és ki a .NET |} Microsoft Docs
description: Hogyan használhatja a tartalom moderátor mérsékelt videók és a .NET ki.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 1/27/2018
ms.author: sajagtap
ms.openlocfilehash: a084b50e44fe26ba2547d0f7b7ed184fb71b190c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347634"
---
# <a name="video-and-transcript-moderation-tutorial"></a>Videó és Beszélgetés szövegének moderálás oktatóanyag

Tartalom moderátor videó API-k lehetővé teszik közepes szintű videók, és hozza létre a videó értékelést az emberi felülvizsgálati eszközben. 

Ezt a részletes útmutató segítséget nyújt annak megértése, hogyan megoldás kiépítését, egy teljes videó és Beszélgetés szövegének moderálás a gép támogatású moderálás és emberi hurok felülvizsgálati létrehozása.

Töltse le a [C# Konzolalkalmazás](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) ehhez az oktatóanyaghoz. A Konzolalkalmazás használja az SDK-t és a kapcsolódó csomagok a következő feladatok végezhetők el:

- A bemeneti video(s) gyorsabb feldolgozás tömörítése
- Közepes szintű pillanatfelvételek és keretek, amelyen a videó
- A keret időbélyegeket segítségével miniatűrök (lemezképek) létrehozása
- Küldje el az időbélyegzőket és a miniatűrök videó értékelést létrehozásához
- A videó beszéd szöveggé alakítani (Beszélgetés szövegének) Media Indexer API-val
- A szöveg moderálás szolgáltatással szövegének mérsékelt
- A videó felülvizsgálati moderált szövegének hozzáadása

## <a name="sample-program-outputs"></a>A minta program kimenete

Mielőtt továbblép, a program a következő minta kimenetek vizsgáljuk meg:

- [Konzol kimeneti](#program-output)
- [Videó áttekintése](#video-review-default-view)
- [Beszélgetés szövegének megtekintése](#video-review-transcript-view)

## <a name="prerequisites"></a>Előfeltételek

1. Regisztrálás a [tartalom moderátor felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com/) webhely és [hozzon létre egyéni címkék](Review-Tool-User-Guide/tags.md) , hogy a kód rendeli hozzá a C# konzolalkalmazást. A következő képernyőn látható az egyéni címkék.

  ![Videó moderálás egyéni címkék](images/video-tutorial-custom-tags.png)

1. Futtassa a mintaalkalmazást, az Azure-fiók és az Azure Media Services-fiók szükséges. Ezenfelül hozzá kell férnie a tartalom moderátor private Preview verziójára. Végezetül kell az Azure Active Directory hitelesítő adatok. Ezek az információk megszerzésére, lásd: [a videó moderálás API gyors üzembe helyezés](video-moderation-api.md).

1. A fájl szerkesztése `App.config` és az Active Directory-bérlő nevét, a Szolgáltatásvégpontok hozzáadása, és előfizetés kulcsok által jelzett `#####`. A következő információk szükségesek:

|Kulcs|Leírás|
|-|-|
|`AzureMediaServiceRestApiEndpoint`|Az Azure Media Services (AMS) API-végpont|
|`ClientSecret`|Azure Media Services előfizetés kulcs|
|`ClientId`|Az Azure Media Services ügyfél-azonosító|
|`AzureAdTenantName`|A szervezet jelölő Active Directory bérlő neve|
|`ContentModeratorReviewApiSubscriptionKey`|A tartalom moderátor előfizetés kulcsa API áttekintése|
|`ContentModeratorApiEndpoint`|A tartalom moderátor API végpontja|
|`ContentModeratorTeamId`|Tartalom moderátor csoport azonosítója|

## <a name="getting-started"></a>Első lépések

Az osztály `Program` a `Program.cs` a fő belépési pont a videó moderálás alkalmazáshoz.

### <a name="methods-of-class-program"></a>Program osztály metódusai

|Módszer|Leírás|
|-|-|
|`Main`|Elemzi a parancssor, felhasználói bevitel összegyűjti, és elindítja a feldolgozása.|
|`ProcessVideo`|Tömöríti, feltölti, módosítja, és létrehozza a videó értékelést.|
|`CreateVideoStreamingRequest`|Videó feltöltése hoz létre|
|`GetUserInputs`|Összegyűjti a felhasználói bevitel; Ha nincs parancssori kapcsolók meglévő használt|
|`Initialize`|Inicializálja az moderálás folyamathoz szükséges objektumok|

### <a name="the-main-method"></a>A fő metódus

`Main()` van, ha végrehajtási indítja el, ezért ezt a lehetőséget a videó moderálás folyamat ismertetése.

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

- Egy MPEG-4 videofájlok moderálás elküldeni tartalmazó könyvtár elérési útja. Minden `*.mp4` moderálás a elküldi ezt a könyvtárat, és annak alkönyvtáraiban található fájlokat.
- Szükség esetén egy logikai (igaz/hamis) jelző jelzi, hogy a szöveg ki céljából moderálással hang hozható létre.

Ha nincs jelen, parancssori argumentumok `Main()` hívások `GetUserInputs()`. Ez a módszer felszólítja a felhasználót, írja be a elérési útját egy videofájl, és adja meg, hogy a szöveg Beszélgetés szövegének generáljon.

> [!NOTE]
> A konzol alkalmazás használja a [Azure Media Indexer API](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) ki generálása a feltöltött videó hang követése. Az eredmények WebVTT formátumban vannak megadva. Ezt a formátumot a további információkért lásd: [webes videó szövegformátum nyomon követi](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API).

### <a name="initialize-and-processvideo-methods"></a>Inicializálás és ProcessVideo módszerek

Függetlenül attól, hogy a program beállításai származnak-e a parancssorból vagy az interaktív felhasználói bevitel `Main()` következő hívások `Initialize()` létrehozása a következő esetekben:

|Osztály|Leírás|
|-|-|
|`AMSComponent`|Mielőtt elküldi őket a moderálás tömöríti a videofájlok lejátszását.|
|`AMSconfigurations`|Az alkalmazás konfigurációs adataihoz, található felület `App.config`.|
|`VideoModerator`| Feltöltése, kódolása, titkosítási és moderálás AMS SDK használatával|
|`VideoReviewApi`|Kezeli a tartalom moderátor szolgáltatásban videó értékelést|

Ezeket az osztályokat (aside a `AMSConfigurations`, ez az egyszerű) Ez az oktatóanyag későbbi szakasza részletesen ismertetnek.

Végezetül videó fájlok feldolgozott egyszerre csak egy meghívásával `ProcessVideo()` minden.

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


A `ProcessVideo()` metódus meglehetősen egyszerű. A következő műveletek sorrendben hajtja végre:

- Tömöríti a videó
- A videó feltölt egy Azure Media Services-eszköz
- A videó mérsékelt egy AMS feladat létrehozása
- Tartalom moderátor hoz létre a videó áttekintése

A következő szakaszok vegye figyelembe a további információkhoz juthat az egyes folyamatok által meghívott `ProcessVideo()`. 

## <a name="compressing-the-video"></a>A videó tömörítése

Hálózati forgalom csökkentése érdekében, hogy az alkalmazás videofájlok H.264 (MPEG-4 AVC) formátumra alakítja át, és 640 képpontos maximális szélessége méretezi őket. A H.264 kodek ajánlott a nagy hatékonyságú (tömörítési arány) miatt. A tömörítés történik használatával az ingyenes `ffmpeg` parancssori eszköz, amely szerepel a `Lib` mappában található a Visual Studio megoldás. A bemeneti fájlok lehet bármely támogatnak a formázás `ffmpeg`, beleértve a leggyakrabban használt videó fájlformátumok és kodekek.

> [!NOTE]
> Amikor elindítja a program parancssori kapcsolók használatával, meg kell adnia moderálás elküldeni a videó fájlokat tartalmazó könyvtár. Minden fájl a könyvtárban, hogy a `.mp4` fájlnév-kiterjesztés dolgoznak fel. Más fájlnév kiterjesztéseinek feldolgozása, frissítse a `Main()` metódus a `Program.cs` a kívánt kiterjesztéseket tartalmazza.

A kód, amely egyetlen videofájl tömöríti a `AmsComponent` osztályt `AMSComponent.cs`. Ez a funkció felelős módszer `CompressVideo()`, itt látható.

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

A kód a következő lépéseket végzi el:

- Ellenőrzi, hogy ellenőrizze, hogy a konfiguráció `App.config` összes szükséges adatot tartalmaz
- Ellenőrzi, hogy ellenőrizze, hogy a `ffmpeg` bináris jelen
- A kimeneti fájl buildek hozzáfűzésével `_c.mp4` alap a fájl nevét (például `Example.mp4`  ->  `E>xample_c.mp4`)
- Az átalakítás végrehajtásához egy parancssori karakterlánc buildek
- Elindul egy `ffmpeg` feldolgozni a parancssor használatával
- A videó feldolgozásra vár

> [!NOTE]
> Ha ismeri a videók már tömörített H.264, és rendelkezik a megfelelő méretű, újraírási `CompressVideo()` kihagyását a tömörítést.

A metódus visszaadja a tömörített kimeneti fájl nevét.

## <a name="uploading-and-moderating-the-video"></a>Feltöltését és moderálás a videó

A videó az Azure Media Services kell tárolni a tartalom moderálás szolgáltatás is feldolgozása előtt. A `Program` osztályt `Program.cs` rövid metódus `CreateVideoStreamingRequest()` , amelyek a videó feltöltéséhez használt adatfolyam-továbbítási kérés képviselő objektum beállítása/beolvasása.

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

A létrejövő `UploadVideoStreamRequest` objektum meghatározott `UploadVideoStreamRequest.cs` (és a szülő `UploadVideoRequest`, a `UploadVideoRequest.cs`). Ezen osztályok nem látható. ezek rövid, és csak azokra a tömörített videó adatok és az arra vonatkozó információval szolgálnak. Egy másik csak adatok osztály `UploadAssetResult` (`UploadAssetResult.cs`) szolgál a feltöltési művelet eredményeinek tárolásához. Most lehet tudni, hogy ezeket a sorokat `ProcessVideo()`:

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
- Állítsa be a kérelem `GenerateVTT` jelzőt, ha a felhasználó által kért egy szöveges Beszélgetés szövegének
- Hívások `CreateAzureMediaServicesJobToModerateVideo()` hajtsa végre a feltöltés, és az eredmény fogadására

## <a name="deep-dive-into-video-moderation"></a>Részletes bemutatója videó moderálás be

A metódus `CreateAzureMediaServicesJobToModerateVideo()` van `VideoModerator.cs`, amely tartalmazza a kódot, amely az Azure Media Services elkészíti a nagy. A metódus forráskód látható a következő kivonat.

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

- A feldolgozás elvégezni egy AMS feladat létrehozása
- Hozzáadja a videofájl kódolás, moderálás, és a szöveg Beszélgetés szövegének generálása feladatok
- Elküldi a feladatot, a fájl- és kezdő feldolgozási feltöltése
- Lekéri a moderálás eredmények, szöveges szövegének (ha szükséges) és egyéb információk

## <a name="sample-video-moderation-response"></a>Videó moderálás mintaválasz

A videó moderálás feladat eredménye (lásd: [videó moderálás gyors üzembe helyezés](video-moderation-api.md) moderálás eredményeit tartalmazó JSON adatstruktúra. Az eredmények a töredék (pillanatfelvételek) tartalmazza a videót, minden egyes tartalmazó események (videóklipeket), amely rendelkezik lett megjelölve, tekintse át a kulcs keretek belül. Minden egyes kulcskocka program pontozza a mennyiségeket által a valószínűsége, hogy felnőtt vagy ellopható tartalmat tartalmazza. A következő példa bemutatja a JSON-választ:

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

Egy, a Videó hangja írjanak elő egyben hozott létre, amikor a `GenerateVTT` jelző be van állítva.

> [!NOTE]
> A konzol alkalmazás használja a [Azure Media Indexer API](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) ki a létrehozni a hang követése a feltöltött videó. Az eredmények WebVTT formátumban vannak megadva. Ezt a formátumot a további információkért lásd: [webes videó szövegformátum nyomon követi](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API).


## <a name="creating-the-human-in-the-loop-review"></a>A HR-részleg hurok felülvizsgálati létrehozása

A moderálás folyamat főbb keretek listáját a videó együtt a Beszélgetés szövegének az a zeneszámok adja vissza. A következő lépés, ha egy tekintse át a tartalom moderátor felülvizsgálati eszközben az emberi moderátorok. Visszatér a `ProcessVideo()` metódus a `Program.cs`, megjelenik a hívást a `CreateVideoReviewInContentModerator()` metódust. Ez a módszer van a `videoReviewApi` osztály, amely `VideoReviewAPI.cs`, és itt láthatók.

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

`CreateVideoReviewInContentModerator()` számos más olyan módszereket hív meg a következő feladatok végezhetők el:

> [!NOTE]
> A konzol alkalmazás használja a [FFmpeg](https://ffmpeg.org/) a miniatűrök létrehozása a könyvtárban. A miniatűrök (lemezképek) felel meg a keret időbélyegek a [videó moderálás kimeneti](#sample-video-moderation-response).

|Tevékenység|Metódusok|Fájl|
|-|-|-|
|A kulcs a videóból keretek, és létrehozza a miniatűr képeket extract|`CreateVideoFrames()`<br>`GenerateFrameImages()`|`FrameGeneratorServices.cs`|
|Szöveg szövegének vizsgálata, ha elérhető, keresse meg a felnőtt vagy ellopható hang|`GenerateTextScreenProfanity()`| `VideoReviewAPI.cs`|
|Készítse elő, és emberi hálózatfelügyeleti videó felülvizsgálati kérelmet elküldte|`CreateReviewRequestObject()`<br> `ExecuteCreateReviewApi()`<br>`CreateAndPublishReviewInContentModerator()`|`VideoReviewAPI.cs`|

## <a name="video-review-default-view"></a>Videó tekintse át az alapértelmezett nézet

A következő képernyő az előző lépések eredményeit jeleníti meg.

![Videó tekintse át az alapértelmezett nézet](images/video-tutorial-default-view.PNG)

## <a name="transcript-generation"></a>Beszélgetés szövegének létrehozása

Ebben az oktatóanyagban szereplő kód eddig a vizuális tartalom fordította. Tekintse át a beszédfelismerés tartalom rendkívül használó, ahogy azt korábban említettük, a beszélgetés a hang előállított szövegének külön és az opcionális folyamat. Most idő vessen egy pillantást szöveg ki létrehozott és használt a felülvizsgálati folyamat hogyan. A szövegének létrehozásának feladatát hárul a [Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-index-content) szolgáltatás.

Az alkalmazás a következő feladatokat hajtja végre:

|Tevékenység|Metódusok|Fájl|
|-|-|-|
|Annak eldöntése, hogy szöveg ki generálása|`Main()`<br>`GetUserInputs()`|`Program.cs`|
|Ha igen, moderálás részeként írjanak elő feladat elküldése|`ConfigureTranscriptTask()`|`VideoModerator.cs`|
|A Beszélgetés szövegének helyi példánya|`GenerateTranscript()`|`VideoModerator.cs`|
|Ez a videó keretek nem megfelelő hang tartalmazó jelző|`GenerateTextScreenProfanity()`<br>`TextScreen()`|`VideoReviewAPI.cs`|
|Az eredmények hozzáadása a áttekintése|`UploadScreenTextResult()`<br>`ExecuteAddTranscriptSupportFile()`|`VideoReviewAPI.cs`|

### <a name="task-configuration"></a>A feladat konfigurációja

Jobb belevágjon a írjanak elő feladat elküldése. `CreateAzureMediaServicesJobToModerateVideo()` (már ismertetett) hívások `ConfigureTranscriptTask()`.

    private void ConfigureTranscriptTask(IJob job)
    {
        string mediaProcessorName = _amsConfigurations.MediaIndexer2MediaProcessor;
        IMediaProcessor processor = _mediaContext.MediaProcessors.GetLatestMediaProcessorByName(mediaProcessorName);

        string configuration = File.ReadAllText(_amsConfigurations.MediaIndexerConfigurationJson);
        ITask task = job.Tasks.AddNew("AudioIndexing Task", processor, configuration, TaskOptions.None);
        task.InputAssets.Add(asset);
        task.OutputAssets.AddNew("AudioIndexing Output Asset", AssetCreationOptions.None);
    }

A Beszélgetés szövegének tevékenység a konfigurációs fájlból olvasható `MediaIndexerConfig.json` a megoldásban `Lib` mappát. AMS eszközök létrehozása a konfigurációs fájl és a kimeneti írjanak elő folyamat. Ha az AMS feladat fut, ez a feladat egy szöveges Beszélgetés szövegének a videofájl hang nyomon követése hoz létre.

> [!NOTE]
> A mintaalkalmazás csak az angol beszéd felismeri.

### <a name="transcript-generation"></a>Beszélgetés szövegének létrehozása

A Beszélgetés szövegének AMS eszközként van közzétéve. A nem kívánt tartalom szövegének vizsgálata, az alkalmazás az eszköz az Azure Media Services tölti le. `CreateAzureMediaServicesJobToModerateVideo()` hívások `GenerateTranscript()`, látható itt, a fájl letöltéséhez.

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

Néhány szükséges AMS telepítést követően végzi el a tényleges letöltési hívás `DownloadAssetToLocal()`, egy általános funkció, amely egy AMS eszköz másolja egy helyi fájlba.

## <a name="transcript-moderation"></a>Beszélgetés szövegének moderálás

Szövegének kezelésére, a vizsgált és a felülvizsgálat alatt használt. A felülvizsgálati létrehozása történik a hatáskörén `CreateVideoReviewInContentModerator()`, adott hívások `GenerateTextScreenProfanity()` a feladat elvégzéséhez. Ez a metódus meghívja `TextScreen()`, amely tartalmazza a legtöbb funkciója. 

`TextScreen()` a következő feladatokat hajtja végre:

- Elemzési alkalommal szövegének tamps és feliratai
- Küldje el a szöveg moderálás minden felirata
- Ez a jelző, amelyek nem kívánt beszéd tartalom kereteket

Vizsgáljuk meg részletesebben ezeket a feladatokat:

### <a name="initialize-the-code"></a>A kód inicializálása

Először inicializálni a változók és a gyűjteményekhez.

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
    

### <a name="parse-the-transcript-for-captions"></a>A feliratok szövegének elemzése

A következő elemezni a feliratok és időbélyegeket VTT formázott szövegének. A felülvizsgálati jelenít meg a feliratok a Beszélgetés szövegének lapon a videó felülvizsgálati képernyőn. Az időbélyeg helyi időre szinkronizálása a feliratok a megfelelő videó keretek szolgálnak.

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

### <a name="moderate-captions-with-the-text-moderation-service"></a>A szöveg moderálás szolgáltatással mérsékelt feliratok

A következő azt az elemzett szöveges feliratok tartalom moderátor szövegre API vizsgálata.

> [!NOTE]
> A tartalom moderátor szolgáltatás kulcs egy kérelmek / második (RPS) sávszélesség-korlátjának rendelkezik. Ha meghaladja a korlátot, az SDK kivételt 429-es jelű hibakóddal. 
>
> Ingyenes szint kulcs egy RPS sávszélesség-korlátjának rendelkezik.

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

### <a name="breaking-down-the-text-moderation-step"></a>A szöveg moderálás lépés bontásban

`TextScreen()` az jelentős módot, így érdemes részletezve van.

1. Először a metódus olvassa be a Beszélgetés szövegének fájl soronként. Üres és sorokat tartalmazó figyelmen kívül hagyja a `NOTE` az vetett bizalmat pontszámot. Az időbélyegzőket és a szöveg tételekhez bontja ki a *kötegek* a fájlban. Köteg szöveg jelöli a hang nyomon követése, és tartalmazza a kezdő és befejező időpontja. A stamp idősorán karakterlánccal kezdődik köteg `-->`. Az azt követő egy vagy több sornyi szöveget.

1. Példányai `CaptionScreentextResult` (definiált `TranscriptProfanity.cs`) minden köteg számváltozókat adatok tárolására szolgálnak.  Ha a rendszer észlelt egy új stamp idősorán, illetve az 1024 karakter hosszú lehet szöveg maximális hossza éri el, egy új `CaptionScreentextResult` hozzáadódik a `csrList`. 

1. A metódus ezután elküldi a minden köteg a szöveg moderálás API. Mindkét meghívja `ContentModeratorClient.TextModeration.DetectLanguageAsync()` és `ContentModeratorClient.TextModeration.ScreenTextWithHttpMessagesAsync()`, amely meghatározott a `Microsoft.Azure.CognitiveServices.ContentModerator` szerelvény. Sebessége korlátozott alatt elkerüléséhez a metódus egy második minden köteg elküldés előtt felfüggeszti.

1. Azután, hogy megérkezett eredmények a szöveg moderálás szolgáltatásból, a metódus ezután elemzi számukra látni enged, hogy megfelelnek-e abban, hogy küszöbértékeket. Ezeket az értékeket a létesített `App.config` , `OffensiveTextThreshold`, `RacyTextThreshold`, és `AdultTextThreshold`. Végezetül a nem kívánt feltételeket, maguk is tárolja. A köteg időtartományban összes kereteket tartalmazó sértő, ellopható, vagy a felnőtt szöveg vannak megjelölve.

1. `TextScreen()` Visszaadja egy `TranscriptScreenTextResult` kapott a videó egy teljes szöveges moderálás eredmény tartalmazó példányra. Ez az objektum nem kívánt tartalmat, minden nemkívánatos feltételeinek együtt a különböző típusú pontszámaihoz, és a jelzőket tartalmaz. A hívó `CreateVideoReviewInContentModerator()`, hívások `UploadScreenTextResult()` , emberi felülvizsgálók szeretné ezt az információt csatlakoztatni az ellenőrzéshez.
 
## <a name="video-review-transcript-view"></a>Videó felülvizsgálati Beszélgetés szövegének megtekintése

A következő képernyő szövegének eredményét moderálás. és a lépéseit mutatja be.

![Videó moderálás Beszélgetés szövegének megtekintése](images/video-tutorial-transcript-view.PNG)

## <a name="program-output"></a>Program kimenete

A program a következő parancssori kimenet látható a különböző feladatok, azok befejezésekor. Emellett a moderálás eredmény (JSON formátumban) és a beszédfelismerés szövegének érhetők el az eredeti videó fájlok könyvtárába.

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

[Töltse le a Visual Studio megoldás](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) példa a fájlok és a szükséges kódtárak ehhez az oktatóanyaghoz, és az integráció a kezdéshez.
