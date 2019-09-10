---
title: 'Példa: Valós idejű videók elemzése – Computer Vision'
titleSuffix: Azure Cognitive Services
description: Megtanulhatja, hogyan végezhet közel valós idejű elemzést egy élő videó-adatfolyam képkockáin a Computer Vision API segítségével.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 25aed0f042050ebadbc6054fcbf0c68dbf782e5e
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70859076"
---
# <a name="how-to-analyze-videos-in-real-time"></a>Videók valós idejű elemzése

Ez az útmutató bemutatja, hogyan végezheti élő videostreamekből származó képkockák közel valós idejű elemzését. Az ilyen rendszerek alapvető összetevői:

- A képkockák beszerzése a videóforrásból
- Az elemezni kívánt képkockák kiválasztása
- A képkockák elküldése az API-ra
- Az API-hívás által visszaadott elemzési eredmények feldolgozása

A kódminták C# nyelven íródtak, a kód a GitHubon a következő címen található: [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

## <a name="the-approach"></a>A módszer

A videostreamek közel valós idejű elemzése többféleképpen oldható meg. Itt most három módszert mutatunk be, növekvő kifinomultsági szinten.

### <a name="a-simple-approach"></a>Az egyszerű módszer

A közel valós idejű elemzési rendszer legegyszerűbben egy végtelen hurokkal alakítható ki, ahol mindegyik iterációban egy új képkockát veszünk, kielemezzük, majd az eredményeket felhasználjuk:

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        AnalysisResult r = await Analyze(f);
        ConsumeResult(r);
    }
}
```

Ha az elemzést egy kisméretű ügyféloldali algoritmus végzi, ez a módszer megfelelő. Amennyiben azonban az elemzés a felhőben történik, az ebben rejlő késés eredményeképp az API-hívások néhány másodpercig is eltarthatnak, amely idő alatt nem rögzítünk képeket, és a szál lényegében semmit nem csinál. A maximális képkocka-sebességet így az API-hívások késése korlátozza.

### <a name="parallelizing-api-calls"></a>Az API-hívások párhuzamosítása

Bár a kisméretű ügyféloldali algoritmusokhoz megfelelő egy egyszerű egyszálas hurok, a felhőalapú API-hívásokhoz a felmerülő késés miatt nem a legmegfelelőbb. A megoldás erre a problémára az, hogy a hosszú futású API-hívásokat párhuzamosan hajtjuk végre a képkockák rögzítésével. A C#-ban ezt a feladatalapú párhuzamos végrehajtás használatával érhetjük el, például így:

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        var t = Task.Run(async () =>
        {
            AnalysisResult r = await Analyze(f);
            ConsumeResult(r);
        }
    }
}
```

Ez a megoldás az elemzéseket egy-egy különálló feladatban indítja el, amelyek tovább futnak a háttérben, amíg mi újabb képkockákat rögzítünk. Ezzel a megoldással elkerüljük a fő szál blokkolását, amíg az API-hívások válaszára várunk, viszont elvesztünk néhány garanciát, amelyeket az egyszerű megoldás biztosított számunkra – vagyis mivel több API-hívás is futhat egyszerre párhuzamosan, az eredmények lehet, hogy rossz sorrendben érkeznek vissza. Ennél a megközelítésnél az is megtörténhet, hogy több szál lép be a ConsumeResult() függvénybe egyidejűleg, ami veszélyes lehet, ha a függvény nem szálbiztos. Végezetül ez a kód nem követi nyomon a létrehozott feladatokat sem, így a kivételek csendben eltűnnek. Ezért utolsó összetevőként hozzá kell adnunk egy „feldolgozó” szálat, amely nyomon követi az elemzési feladatokat, kezeli a kivételeket, lelövi a hosszan futó feladatokat, és biztosítja, hogy az eredmények a megfelelő sorrendben, egyenként legyenek feldolgozva.

### <a name="a-producer-consumer-design"></a>Az előállító-fogyasztó alapú megközelítés

A végső "előállító-fogyasztó" alapú rendszerünkben van egy előállító szál, amely a korábbi végtelen ciklusunkhoz hasonlít. Azonban ahelyett, hogy az érkező elemzési eredményeket azonnal feldogozná, az előállító csupán egy sorba rakja a feladatokat, hogy azokat nyomon követhesse.

```csharp
// Queue that will contain the API call tasks.
var taskQueue = new BlockingCollection<Task<ResultWrapper>>();

// Producer thread.
while (true)
{
    // Grab a frame.
    Frame f = GrabFrame();

    // Decide whether to analyze the frame.
    if (ShouldAnalyze(f))
    {
        // Start a task that will run in parallel with this thread.
        var analysisTask = Task.Run(async () =>
        {
            // Put the frame, and the result/exception into a wrapper object.
            var output = new ResultWrapper(f);
            try
            {
                output.Analysis = await Analyze(f);
            }
            catch (Exception e)
            {
                output.Exception = e;
            }
            return output;
        }

        // Push the task onto the queue.
        taskQueue.Add(analysisTask);
    }
}
```

Emellett rendelkezünk egy feldolgozó szállal, amely kiveszi a feladatokat az üzenetsorból, megvárja, amíg befejeződnek, és megjeleníti az eredményeket vagy kezeli a felmerült kivételeket. Az üzenetsor használatával biztosíthatjuk, hogy az eredmények egyesével, a megfelelő sorrendben lesznek feldolgozva, a rendszer maximális képkocka-sebességének korlátozása nélkül.

```csharp
// Consumer thread.
while (true)
{
    // Get the oldest task.
    Task<ResultWrapper> analysisTask = taskQueue.Take();
 
    // Await until the task is completed.
    var output = await analysisTask;

    // Consume the exception or result.
    if (output.Exception != null)
    {
        throw output.Exception;
    }
    else
    {
        ConsumeResult(output.Analysis);
    }
}
```

## <a name="implementing-the-solution"></a>A megoldás megvalósítása

### <a name="getting-started"></a>Első lépések

Az alkalmazás minél gyorsabb üzembe állítása érdekében a fent leírt rendszert valósítottuk meg, azzal a céllal, hogy kellő rugalmasságot biztosítson a különböző forgatókönyvek megvalósításához, mégis egyszerű legyen a használata. A kódot a következő helyen éri el: [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis).

A kódtár tartalmazza a FrameGrabber osztályt, amellyel megvalósítható a fent leírt létrehozó-feldolgozó rendszer egy webkamera által szolgáltatott videoképkockák feldolgozására. A felhasználó határozhatja meg az API-hívások pontos formáját, és az osztály események használatával tudatja a hívó kóddal, mikor érkeztek az új képkockák, illetve hogy mikor válnak elérhetővé az új elemzési eredmények.

Az egyes lehetőségek bemutatására a kódtárat használó két mintaalkalmazás érhető el. Az első egy egyszerű konzolalkalmazás, amelynek az egyszerűsített verzióját az alábbiakban reprodukáljuk. Az alkalmazás begyűjti a képkockákat az alapértelmezett webkameráról, és továbbítja azokat a Face API-ra arcfelismerésre.

```csharp
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;
using VideoFrameAnalyzer;

namespace BasicConsoleSample
{
    internal class Program
    {
        const string ApiKey = "<your API key>";
        const string Endpoint = "https://<your API region>.api.cognitive.microsoft.com";

        private static void Main(string[] args)
        {
            // Create grabber.
            FrameGrabber<DetectedFace[]> grabber = new FrameGrabber<DetectedFace[]>();

            // Create Face API Client.
            FaceClient faceClient = new FaceClient(new ApiKeyServiceClientCredentials(ApiKey))
            {
                Endpoint = Endpoint
            };

            // Set up a listener for when we acquire a new frame.
            grabber.NewFrameProvided += (s, e) =>
            {
                Console.WriteLine($"New frame acquired at {e.Frame.Metadata.Timestamp}");
            };

            // Set up Face API call.
            grabber.AnalysisFunction = async frame =>
            {
                Console.WriteLine($"Submitting frame acquired at {frame.Metadata.Timestamp}");
                // Encode image and submit to Face API.
                return (await faceClient.Face.DetectWithStreamAsync(frame.Image.ToMemoryStream(".jpg"))).ToArray();
            };

            // Set up a listener for when we receive a new result from an API call.
            grabber.NewResultAvailable += (s, e) =>
            {
                if (e.TimedOut)
                    Console.WriteLine("API call timed out.");
                else if (e.Exception != null)
                    Console.WriteLine("API call threw an exception.");
                else
                    Console.WriteLine($"New result received for frame acquired at {e.Frame.Metadata.Timestamp}. {e.Analysis.Length} faces detected");
            };

            // Tell grabber when to call API.
            // See also TriggerAnalysisOnPredicate
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running in the background.
            grabber.StartProcessingCameraAsync().Wait();

            // Wait for key press to stop.
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();

            // Stop, blocking until done.
            grabber.StopProcessingAsync().Wait();
        }
    }
}
```

A második mintaalkalmazás egy kicsit érdekesebb, és ebben kiválasztható, hogy melyik API-t hívja meg a videóképeken. A bal oldalon az alkalmazás az élő videó előnézetét jeleníti meg, a jobb oldalon pedig a legújabb API-eredményéket jeleníti meg a megfelelő képkockára vetítve.

A legtöbb módban látható késés mutatkozik a bal oldali élő videókép és a jobb oldalon megjelenített eredmények közt. Ez a késés az API-kérés feldolgozásának időtartamával egyezik. Ez alól kivételt az „EmotionsWithClientFaceDetect” mód képez, amely helyileg is végez arcfelismerést az ügyfélszámítógépen az OpenCV használatával, mielőtt beküldené a képeket a Cognitive Services szolgáltatásnak. Ezáltal azonnal megjeleníthető a felismert arc, majd az érzelmekkel később kiegészíthető, ha megérkezik az API-hívás eredménye. Ez az alkalmazás a „hibrid” megközelítés lehetőségét mutatja, ahol az egyszerű feldolgozási feladatok elvégezhetők az ügyfélen, majd a Cognitive Services API-k használatával ez szükség szerint kiegészíthető további speciális elemzési műveletekkel.

![Képernyőkép a LiveCameraSample-alkalmazásról, amely egy megjelenített címkékkel rendelkező képet mutat](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>A kódbázisba való integráció

A minta használatához hajtsa végre az alábbi lépéseket:

1. A Vision API-k API-kulcsainak beszerzése az [előfizetésekből](https://azure.microsoft.com/try/cognitive-services/). A videoképkockák elemzéséhez megfelelő API-k:
    - [Computer Vision API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [Face API](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. Klónozza a [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) GitHub-adattárat

3. Nyissa meg a mintát a Visual Studio 2015-es vagy újabb verziójában, majd hozza létre és futtassa a minta alkalmazásokat:
    - A BasicConsoleSample esetében a Face API-kulcs nem változtatható módon rögzítve van közvetlenül a [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs) fájlban.
    - A LiveCameraSample esetében a kulcsokat az alkalmazás Settings (Beállítások) panelén kell megadni. A kulcsokat a rendszer felhasználói adatként a munkamenetek közt is megőrzi.

Amikor készen áll az integrációra, **egyszerűen hivatkozzon a VideoFrameAnalyzer kódtárra a projektjeiben.**

A VideoFrameAnalyzer kép-, hang-, videó- és szövegfelismerési képességei az Azure Cognitive Servicesen alapulnak. A feltöltött képeket, hangokat, videókat és egyéb adatokat a Microsoft fogadja (az alkalmazáson keresztül), és a szolgáltatás fejlesztése céljával azokat felhasználhatja. Szükségünk van az Ön közreműködésére az alkalmazás által az Azure Cognitive Services szolgáltatásba küldött adatok tulajdonosainak védelmében.

## <a name="summary"></a>Összegzés

Ebben az útmutatóban megtanulta, hogyan futtathat közel valós idejű elemzést az élő videó streameken az arc és a Computer Vision API-k használatával, és hogy miként használható a mintakód a kezdéshez. Az alkalmazások összeállításának megkezdéséhez [az Azure Cognitive Services regisztrációs oldalán](https://azure.microsoft.com/try/cognitive-services/) talál ingyenes API-kulcsokat.

Visszajelzéseket és javaslatokat a [GitHub-adattárban](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/), vagy az API-kkal kapcsolatos általánosabb visszajelzések esetén a [UserVoice webhelyén](https://cognitive.uservoice.com/) adhat.

