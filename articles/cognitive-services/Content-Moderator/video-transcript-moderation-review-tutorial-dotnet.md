---
title: 'Oktatóanyag: Mérsékelt videók és a .NET - Content Moderator szövegekben'
titlesuffix: Azure Cognitive Services
description: Ez az oktatóanyag segítségével megismerheti, hogyan hozhat létre egy teljes videó és a szöveges moderálás megoldás a gépi támogatású képmoderálás és emberi hurok felülvizsgálat létrehozása.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: pafarley
ms.openlocfilehash: fc49081c765834a0ed0e5199923606ced7daa081
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522077"
---
# <a name="tutorial-video-and-transcript-moderation"></a>Oktatóanyag: A videó és a szöveges moderálása

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy teljes videó és a szöveges moderálás megoldás a gépi támogatású képmoderálás és emberi hurok felülvizsgálat létrehozása.

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> - A bemeneti videó(k) tömörítése gyorsabb feldolgozáshoz
> - A videó moderálása jelenetek és képkockák elemzéséhez
> - Miniatűrök (képek) létrehozása időbélyegekkel
> - Videoértékelések készítése időbélyegek és miniatűrök beküldésével
> - A videó szövegének írott szöveggé alakítása (átiratkészítés) a Media Indexer API-val
> - Az átirat moderálása a szövegmoderálási szolgáltatással
> - A moderált átiratok hozzáadása a videoértékeléshez

## <a name="prerequisites"></a>Előfeltételek

- Regisztráljon a [Content Moderator felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com/) webhely, és hozzon létre egyéni címkék. Lásd: [címkékkel](Review-Tool-User-Guide/tags.md) Ha ehhez a lépéshez segítségre van szüksége.

    ![Képernyőkép a videó moderálás egyéni címkék](images/video-tutorial-custom-tags.png)
- Futtassa a mintaalkalmazást, szüksége van egy Azure-fiókra, egy Azure Media Services-erőforrás, egy Azure Content Moderator erőforrás és az Azure Active Directorybeli hitelesítő adatokat. Ezek kapcsolatos utasításokért lásd: a [videó moderálási API](video-moderation-api.md) útmutató.
- Töltse le a [videó felülvizsgálati Konzolalkalmazás](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) a Githubon.

## <a name="enter-credentials"></a>Hitelesítő adatok megadása

Szerkessze a `App.config` fájlt, és az Active Directory-bérlő nevét, a Szolgáltatásvégpontok és előfizetési kulcsok által jelzett `#####`. A következő adatokra lesz szüksége:

    |Kulcs|Leírás|
    |-|-|
    |`AzureMediaServiceRestApiEndpoint`|Az Azure Media Services (AMS) API végpontja|
    |`ClientSecret`|Az Azure Media Services előfizetési kulcsa|
    |`ClientId`|Az Azure Media Services ügyfélazonosítója|
    |`AzureAdTenantName`|A szervezetet képviselő Active Directory-bérlő neve|
    |`ContentModeratorReviewApiSubscriptionKey`|A Content Moderator felügyeleti API előfizetési kulcsa|
    |`ContentModeratorApiEndpoint`|A Content Moderator API végpontja|
    |`ContentModeratorTeamId`|A tartalommoderátori csapat azonosítója|

## <a name="examine-the-main-code"></a>A fő kódok vizsgálatát

A `Program` osztály a `Program.cs` fájlban a videomoderálási alkalmazás fő belépési pontja.

### <a name="methods-of-program-class"></a>Program osztály metódusain

|Módszer|Leírás|
|-|-|
|`Main`|Elemzi a parancssort, felhasználói adatokat gyűjt, és megkezdi a feldolgozást.|
|`ProcessVideo`|Videoértékeléseket tömörít, tölt fel és moderál.|
|`CreateVideoStreamingRequest`|Videofeltöltési streamet hoz létre|
|`GetUserInputs`|Felhasználói adatokat gyűjt; hiányzó parancssori opciók esetén használandó|
|`Initialize`|Inicializálja a moderálási folyamathoz szükséges objektumokat|

### <a name="the-main-method"></a>A Main módszer

A `Main()` a végrehajtás első fázisa, így a videomoderálási folyamat ismertetését is ezzel kezdjük.

[!code-csharp[Main](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=20-24,42-52,54-74)]

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

[!code-csharp[ProcessVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=76-118)]

A `ProcessVideo()` metódus egyszerűen működik. A következő műveleteket hajtja végre, ebben a sorrendben:

- Tömöríti a videót
- Feltölti a videót egy Azure Media Services-eszközre
- Létrehoz egy AMS-feladatot a videó moderálásához
- Létrehoz egy videóértékelést a Content Moderator szolgáltatásban

A következő szakaszok a `ProcessVideo()` által meghívott folyamatokat részletezik. 

## <a name="compress-the-video"></a>A videó tömörítése

A hálózati forgalom minimalizálása érdekében az alkalmazás H.264 (MPEG-4 AVC) formátumba konvertálja a videofájlokat, és 640 képpontos maximum szélességűre méretezi őket. A H.264 kodek rendkívül hatékony (magas tömörítési aránnyal bír), ezért javasolt a használata. A tömörítés az ingyenes `ffmpeg` parancssori eszközzel történik, amely a Visual Studio szolgáltatás `Lib` mappájában található. A bemeneti fájlok bármilyen, `ffmpeg` által támogatott formátumúak lehetnek, beleértve a leggyakrabban használt videoformátumokat és kodekeket.

> [!NOTE]
> Amikor a parancssorral indítja el a programot, megadja a videofájlokat tartalmazó könyvtárat, amelyet moderálásra küld. A program a könyvtár összes, `.mp4` kiterjesztésű fájlját feldolgozza. A fájlkiterjesztések feldolgozásához frissítse úgy a `Program.cs` `Main()` metódusát, hogy az tartalmazza a kívánt bővítményeket.

Az egyetlen videofájlt tömörítő kód az `AMSComponent.cs` `AmsComponent` osztálya. Ezért a funkcióért a `CompressVideo()` metódus felelős, amely itt látható.

[!code-csharp[CompressVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/AMSComponent.cs?range=31-59)]

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

## <a name="upload-and-moderate-the-video"></a>Fel- és közepes szintű a videót

Ahhoz, hogy egy videót feldolgozhasson a Content Moderation szolgáltatással, az Azure Media Servicesben kell tárolni. A `Program.cs` `Program` osztály egy `CreateVideoStreamingRequest()` rövid metódust tartalmaz, amely a videó feltöltéséhez használt streamelési kérelmet képviselő objektumot adja vissza.

[!code-csharp[CreateVideoStreamingRequest](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=120-133)]

A létrejövő `UploadVideoStreamRequest` objektum az `UploadVideoStreamRequest.cs` fájlban van definiálva (a szülője, `UploadVideoRequest`, pedig az `UploadVideoRequest.cs` fájlban). Ezek az osztályok itt nem jelennek meg; rövidek, és egyetlen céljuk a tömörített videók adatainak tárolása. A feltöltési folyamat eredményeit egy másik kizárólag adatokat tároló osztály, az `UploadAssetResult` (`UploadAssetResult.cs`) tartalmazza. Most már képes értelmezni a `ProcessVideo()` sorait:

[!code-csharp[ProcessVideoSnippet](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=91-104)]

Ezek a sorok a következő feladatokat hajtják végre:

- Létrehoz egy `UploadVideoStreamRequest` parancsot a tömörített videó feltöltéséhez
- Beállítja a kérés `GenerateVTT` jelölőjét, ha a felhasználó szöveges átiratot kért
- Az `CreateAzureMediaServicesJobToModerateVideo()` parancsot meghívva elvégzi a feltöltést és megkapja az eredményt

## <a name="examine-video-moderation-code"></a>Vizsgálja meg a kódot videomoderálás

A `CreateAzureMediaServicesJobToModerateVideo()` metódus a `VideoModerator.cs` fájlban található, amely az Azure Media Services szolgáltatással kommunikáló kódok nagy részét tartalmazza. A metódus forráskódja a következő kivonatban látható.

[!code-csharp[CreateAzureMediaServicesJobToModerateVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=230-283)]

A kód a következő feladatokat hajtja végre:

- Létrehoz egy AMS-feladatot a feldolgozáshoz
- Hozzáadja a videofájl kódolásához, moderálásához és szöveges átiratának elkészítéséhez szükséges feladatokat
- Elküldi a feladatot, feltölti a fájlt és megkezdi a feldolgozást
- Lekéri a moderálás eredményeit, a szöveges átiratot (amennyiben a felhasználó kérte), és egyéb adatokat

## <a name="sample-video-moderation-output"></a>Videomoderálás kimeneti példa

A videomoderálási feladat eredménye (lásd: [videomoderálás gyors útmutatója](video-moderation-api.md)) egy JSON-adatstruktúra, amely a moderálás eredményeit tartalmazza. Az eredmények részletezik a videó töredékeit (jeleneteit), amelyek kulcskockákból álló, felülvizsgálatra megjelölt eseményeket (klipeket) tartalmaznak. A kulcskockák aszerint vannak pontozva, hogy milyen valószínűséggel tartalmaznak felnőtt vagy kényes tartalmat. A következő példa egy JSON-válasz:

```json
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
```

A `GenerateVTT` jelölő használatakor hangátirat is készül.

> [!NOTE]
> A konzolalkalmazás az [Azure Media Indexer API-val](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) átiratokat készít a feltöltött videó hangsávjából. Az eredmények WebVTT formátumban készülnek el. További információt erről a formátumról a [webes videók szövegsávos formátumáról](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API) szóló cikkben találhat.

## <a name="create-a-the-human-in-the-loop-review"></a>Hozzon létre egy az emberi hurok áttekintése

A moderálási folyamat visszaadja a videó kulcskockáinak listáját, valamint a hangsávok átiratát. A következő lépés egy emberi moderátoroknak szóló értékelés elkészítése a Content Moderator felülvizsgálati eszközben. Ha visszatér a `Program.cs` `ProcessVideo()` metódusához, láthatja a `CreateVideoReviewInContentModerator()` metódus meghívását. Ez a metódus a `videoReviewApi` osztályba tartozik, amely a `VideoReviewAPI.cs` fájlban található, és itt látható.

[!code-csharp[CreateVideoReviewInContentModerator](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=42-69)]

A `CreateVideoReviewInContentModerator()` számos egyéb metódust meghív a következő feladatok elvégzéséhez:

> [!NOTE]
> A konzolalkalmazás az [FFmpeg](https://ffmpeg.org/) könyvtárat használja a miniatűrök létrehozásához. Ezek a miniatűrök (képek) a videomoderálás kimenetben keret időbélyegei felelnek meg.

|Tevékenység|Metódusok|Fájl|
|-|-|-|
|Kinyeri a kulcskockákat a videóból, és miniatűr képeket hoz létre róluk|`CreateVideoFrames()`<br>`GenerateFrameImages()`|`FrameGeneratorServices.cs`|
|Átvizsgálja a szöveges átiratot (amennyiben elérhető), és felnőtt vagy kényes hangtartalmat keres|`GenerateTextScreenProfanity()`| `VideoReviewAPI.cs`|
|Előkészít és beküld egy videoértékelést emberi vizsgálatra|`CreateReviewRequestObject()`<br> `ExecuteCreateReviewApi()`<br>`CreateAndPublishReviewInContentModerator()`|`VideoReviewAPI.cs`|

A következő kép egy az előző lépések eredményeit mutatja.

![Videoértékelés – alapértelmezett nézet](images/video-tutorial-default-view.PNG)

## <a name="process-the-transcript"></a>Az átirat folyamat

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

[!code-csharp[ConfigureTranscriptTask](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=295-304)]

A program az átírási feladat konfigurációját a `Lib` mappa `MediaIndexerConfig.json` fájljából olvassa be. Létrejönnek a konfigurációs fájl és az átírási folyamat kimenetének AMS-eszközei. Az AMS-feladat futása alatt a feladat létrehoz egy szöveges átiratot a videofájl hangsávjából.

> [!NOTE]
> A mintaalkalmazás csak az amerikai angol nyelvű szöveget ismeri fel.

### <a name="transcript-generation"></a>Átiratkészítés

Az átirat AMS-eszközként lesz közzétéve. Az átirat kifogásolható tartalmak utáni vizsgálatához az alkalmazás letölti az eszközt az Azure Media Services szolgáltatásból. A `CreateAzureMediaServicesJobToModerateVideo()` meghívja az itt látható `GenerateTranscript()` parancsot, hogy lekérje a fájlt.

[!code-csharp[GenerateTranscript](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=351-370)]

Az AMS szükséges beállítása után a letöltést a `DownloadAssetToLocal()` parancs hívásával indíthatja el, amely egy általános, AMS-eszközöket egy helyi fájlba másoló függvény.

## <a name="moderate-the-transcript"></a>Az átirat mérsékelt

A kész átiratokat beolvasva felhasználhatja őket az értékeléshez. Az értékelés létrehozása a `CreateVideoReviewInContentModerator()` parancs feladata, amely a `GenerateTextScreenProfanity()` parancsot hívja meg a feladathoz. Ez a metódus ezután a `TextScreen()` parancsot hívja meg, amely a legtöbb funkciót tartalmazza.

A `TextScreen()` a következő feladatokat hajtja végre:

- Az átirat elemzése időbélyegek és feliratok kereséséhez
- Feliratok beküldése szövegmoderálásra
- Potenciálisan kifogásolható beszédtartalmat tartalmazó képkockák megjelölése

Vizsgáljuk meg részletesebben ezeket a feladatokat:

### <a name="initialize-the-code"></a>A kód inicializálása

Első lépésként inicializálja az összes változót és gyűjteményt.

[!code-csharp[TextScreen](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=515-527)]

### <a name="parse-the-transcript-for-captions"></a>Az átirat elemzése feliratok kereséséhez

Következő lépésként elemezze a VTT formátumú átiratot feliratok és időbélyegek kereséséhez. A felülvizsgálati eszköz ezeket a feliratokat a videoértékelési képernyő Átirat lapján jeleníti meg. Az időbélyegek a feliratok a megfelelő képkockákkal való szinkronizálását szolgálják.

[!code-csharp[TextScreen2](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=528-567)]

### <a name="moderate-captions-with-the-text-moderation-service"></a>Felirat moderálása a szövegmoderálási szolgáltatással

Következő lépésként beolvassuk az elemzett szöveges feliratokat a Content Moderator szöveges API-jával.

> [!NOTE]
> A Content Moderator szolgáltatáskulcs egy másodpercenkénti kérelmekre (RPS-re) vonatkozó korláttal rendelkezik. Ha túllépi ezt a korlátot, az SDK 429-es hibakódú kivételt jelez.
>
> Az ingyenes szint kulcsának a sebességkorlátja egy RPS.

[!code-csharp[TextScreen3](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=568-653)]

### <a name="text-moderation-breakdown"></a>Szöveg moderálása lebontása

A `TextScreen()` egy összetett metódus, ezért részletesen ismertetjük.

1. A metódus először soronként elolvassa az átiratot. Az üres sorokat és megbízhatósági pontszámmal rendelkező `NOTE`-elemeket figyelmen kívül hagyja. Kinyeri az időbélyegzőket és szöveges elemeket a fájl *jeleiből*. A jelek a hangsáv szövegét képviselik, és kezdési és befejezési idővel rendelkeznek. A jelek a(z) `-->` sztringet tartalmazó időbélyegvonallal kezdődnek. Ezt egy vagy több sornyi szöveg követi.

1. A (`TranscriptProfanity.cs` elemben meghatározott) `CaptionScreentextResult` példányai tárolják az egyes jelek elemzett adatait.  Új időbélyegsor észlelésekor vagy a maximális szöveghossz (1024 karakter) elérésekor a program új `CaptionScreentextResult` elemet ad hozzá a következőhöz: `csrList`. 

1. A metódus ezután elküldi a jeleket a Text Moderation API-nak. Meghívja a `ContentModeratorClient.TextModeration.DetectLanguageAsync()` és a `ContentModeratorClient.TextModeration.ScreenTextWithHttpMessagesAsync()` függvényeket, amelyek a `Microsoft.Azure.CognitiveServices.ContentModerator` szerelvényben vannak definiálva. A sebességkorlátozás elkerülése érdekében a metódus minden jel beküldése előtt egy másodperc szünetet tart.

1. Miután megkapja a Text Moderation szolgáltatás eredményeit, a metódus elemzi azokat, és megállapítja, hogy megfelelnek-e a megbízhatósági küszöbértékeknek. Ezek az értékek az `App.config` fájlban, a következőkként vannak meghatározva: `OffensiveTextThreshold`, `RacyTextThreshold` és `AdultTextThreshold`. A szolgáltatás végül tárolja magukat a kifogásolható kifejezéseket is. A jel időtartományán belül minden képkockát sértő, kényes és/vagy felnőtt szövegként jelöl meg.

1. A `TextScreen()` egy `TranscriptScreenTextResult` példányt eredményez, amely a teljes videó szövegmoderálásának eredményét tartalmazza. Ez az objektum a különböző típusú kifogásolható tartalmak jelölőit és pontszámait tartalmazza, valamint az összes kifogásolható kifejezést. A hívó (`CreateVideoReviewInContentModerator()`) meghívja az `UploadScreenTextResult()` parancsot a fenti adatok az értékeléshez való csatolásához, hogy azok az emberi felülvizsgálat során is elérhetők legyenek.

A következő kép az átiratkészítés és a moderálási lépések eredményét jeleníti meg.

![Videomoderálás – átiratnézet](images/video-tutorial-transcript-view.PNG)

## <a name="program-output"></a>A program kimenete

A program következő parancssori kimenete a különböző végrehajtott feladatokat mutatja. Emellett a moderálás (JSON formátumú) eredménye és a beszédátirat is elérhető ugyanabban a könyvtárban, ahol az eredeti videofájlok.

```console
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
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban beállította egy alkalmazás, amely módosítja a videóját&mdash;beleértve a szöveges tartalom&mdash;, és a vizsgálóeszközt felülvizsgálatok hoz létre. Ezután további információ a videomoderálás részleteit.

> [!div class="nextstepaction"]
> [Videomoderálás](./video-moderation-human-review.md)
