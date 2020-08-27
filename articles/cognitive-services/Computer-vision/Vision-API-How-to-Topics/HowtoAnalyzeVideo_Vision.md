---
title: Videók elemzése közel valós időben – Computer Vision
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan végezheti el a közel valós idejű elemzést olyan kereteken, amelyek egy élő videó streamből származnak a Computer Vision API használatával.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: c86b06ff46f1ddc8d22d2ab7ec4bc8620a8c862f
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88933171"
---
# <a name="analyze-videos-in-near-real-time"></a>Videók elemzése közel valós időben

Ez a cikk bemutatja, hogyan végezheti el a közel valós idejű elemzést olyan kereteken, amelyek egy élő videó streamből származnak a Computer Vision API használatával. Az elemzés alapvető elemei a következők:

- Képkockák beszerzése a videó forrásaként.
- Válassza ki az elemezni kívánt képkockákat.
- A keretek elküldése az API-nak.
- Az API-hívás által visszaadott elemzési eredmények fogyasztása.

A cikkben szereplő minták a C# nyelven íródnak. A kód eléréséhez nyissa meg a [video frame Analysis Sample](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) oldalt a githubon.

## <a name="approaches-to-running-near-real-time-analysis"></a>A közel valós idejű elemzések futtatásának módszerei

A közel valós idejű elemzések a videó streameken való futtatásának problémáját a különböző megközelítések segítségével lehet megoldani. Ez a cikk háromat vázol fel a kifinomultság szintjének növelésével.

### <a name="design-an-infinite-loop"></a>Végtelen hurok kialakítása

A közel valós idejű elemzéshez a legegyszerűbb kialakítás egy végtelen hurok. A hurok minden egyes iterációjában megragad egy keretet, elemezheti, majd felhasználhatja az eredményt:

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

Ha az elemzés egy egyszerű, ügyféloldali algoritmust tartalmaz, akkor ez a megközelítés megfelelő lenne. Ha azonban az elemzés a felhőben történik, az eredményül kapott késés azt jelenti, hogy egy API-hívás több másodpercig is eltarthat. Ebben az időszakban nem rögzíti a képeket, és a szál lényegében semmit sem csinál. A maximális képkockasebességet az API-hívások késése korlátozza.

### <a name="allow-the-api-calls-to-run-in-parallel"></a>Az API-hívások párhuzamos futtatásának engedélyezése

Bár egy egyszerű, egyszálas hurok logikus, ügyféloldali algoritmus, nem jól illeszkedik a Felhőbeli API-hívások késéséhez. A probléma megoldásának lehetővé tétele, hogy a hosszan futó API-hívás párhuzamosan fusson a frame-rángatás használatával. A C#-ban ez a feladat-alapú párhuzamosság használatával végezhető el. Futtathatja például a következő kódot:

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

Ezzel a módszerrel elindíthatja az egyes elemzéseket egy külön feladatban. A feladat futhat a háttérben, miközben folytatja az új keretek olvasását. A módszer elkerüli a fő szál blokkolását, ahogy várnia kell egy API-hívás visszaküldésére. A megközelítés azonban bizonyos hátrányokat jelenthet:
* A szolgáltatás néhány garanciát nyújt a megadott egyszerű verzióra. Ez azt eredményezi, hogy több API-hívás is előfordulhat párhuzamosan, és az eredmények nem megfelelő sorrendben kerülnek vissza. 
* Azt is okozhatja, hogy több szál is megadhatja a ConsumeResult () függvényt egyszerre, ami veszélyes lehet, ha a függvény nem a szál számára biztonságos. 
* Végezetül ez az egyszerű kód nem tartja nyilván a létrehozott feladatokat, ezért a kivételek csendesen eltűnnek. Ezért hozzá kell adnia egy "fogyasztói" szálat, amely nyomon követi az elemzési feladatokat, megnöveli a kivételeket, megöli a hosszan futó feladatokat, és gondoskodik arról, hogy az eredmények megfelelő sorrendben legyenek felhasználva, egy időben.

### <a name="design-a-producer-consumer-system"></a>Termelő – fogyasztói rendszer tervezése

Végső megközelítésként a "termelő-fogyasztói" rendszer kialakításával olyan termelői szálat hozhat létre, amely a korábban említett végtelen hurokhoz hasonlóan néz ki. Az elemzési eredményeknek a rendelkezésre állása helyett azonban a gyártó egyszerűen egy várólistán helyezi el a feladatokat, hogy nyomon kövessék őket.

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

Létrehoz egy felhasználói szálat is, amely felveszi a várólistáról a feladatokat, megvárja, amíg befejeződik, és vagy megjeleníti az eredményt, vagy megnöveli a dobott kivételt. A várólista használatával garantálhatja, hogy az eredmények egy időben, a megfelelő sorrendben, a rendszer maximális képkockasebességének korlátozása nélkül legyenek felhasználva.

```csharp
// Consumer thread.
while (true)
{
    // Get the oldest task.
    Task<ResultWrapper> analysisTask = taskQueue.Take();
 
    // Wait until the task is completed.
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

## <a name="implement-the-solution"></a>A megoldás implementálása

### <a name="get-started-quickly"></a>Gyors kezdés

Annak érdekében, hogy az alkalmazás a lehető leggyorsabban elérhető legyen, az előző szakaszban ismertetett rendszert Implementáljuk. Ennek a célja, hogy rugalmasan alkalmazkodjon a sok forgatókönyvhöz, miközben könnyen használható. A kód eléréséhez nyissa meg a [video frame Analysis Sample](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) oldalt a githubon.

A könyvtár tartalmazza a `FrameGrabber` osztályt, amely a korábban tárgyalt gyártó – fogyasztói rendszer segítségével dolgozza fel a képkockákat egy webkamera használatával. A felhasználók megadhatják az API-hívás pontos formáját, és az osztály az eseményeket használva lehetővé teszi, hogy a hívó kód megismerje az új keret beszerzését, vagy ha elérhetővé vált egy új elemzési eredmény.

Néhány lehetőség kimutatása érdekében két, a könyvtárat használó alkalmazást adtunk meg. 

Az első minta alkalmazás egy egyszerű konzolos alkalmazás, amely a képkockákat az alapértelmezett webhelyről fogadja, majd elküldi őket a Face szolgáltatásba Arcfelismerés céljából. Az alkalmazás egyszerűsített verziója a következő kódban jön létre:

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

        private static async Task Main(string[] args)
        {
            // Create grabber.
            FrameGrabber<DetectedFace[]> grabber = new FrameGrabber<DetectedFace[]>();

            // Create Face Client.
            FaceClient faceClient = new FaceClient(new ApiKeyServiceClientCredentials(ApiKey))
            {
                Endpoint = Endpoint
            };

            // Set up a listener for when we acquire a new frame.
            grabber.NewFrameProvided += (s, e) =>
            {
                Console.WriteLine($"New frame acquired at {e.Frame.Metadata.Timestamp}");
            };

            // Set up a Face API call.
            grabber.AnalysisFunction = async frame =>
            {
                Console.WriteLine($"Submitting frame acquired at {frame.Metadata.Timestamp}");
                // Encode image and submit to Face service.
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

            // Tell grabber when to call the API.
            // See also TriggerAnalysisOnPredicate
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running in the background.
            await grabber.StartProcessingCameraAsync();

            // Wait for key press to stop.
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();

            // Stop, blocking until done.
            await grabber.StopProcessingAsync();
        }
    }
}
```

A második minta alkalmazás egy kicsit érdekesebb. Lehetővé teszi, hogy kiválassza, melyik API-t hívja meg a videó kereteken. A bal oldalon az alkalmazás az élő videó előnézetét jeleníti meg. A jobb oldalon a legújabb API-eredményt fedi le a megfelelő kereten.

A legtöbb mód esetében a bal oldali élő videó és a jobb oldali vizualizációs elemzés között látható a késés. Ez a késleltetés az API-hívás végrehajtásához szükséges idő. Kivételt képeznek a "EmotionsWithClientFaceDetect" üzemmód, amely az OpenCV a helyi számítógépen végzi el az észlelést, mielőtt beküldi a lemezképeket az Azure Cognitive Servicesba. 

Ennek a módszernek a használatával azonnal megjelenítheti az észlelt arcot. Ezután később is frissítheti az érzelmeket, miután az API-hívást visszaadja. Ez a "hibrid" megközelítés lehetőségét mutatja be. Ez azt is megteheti, hogy egy egyszerű feldolgozást végeznek az ügyfélen, majd Cognitive Services API-k a feldolgozás kibővítéséhez, ha szükséges, összetettebb elemzést kell alkalmazni.

![A címkével ellátott képet megjelenítő LiveCameraSample alkalmazás](../../Video/Images/FramebyFrame.jpg)

### <a name="integrate-the-samples-into-your-codebase"></a>A minták integrálása a saját kódba

A minta megkezdéséhez tegye a következőket:

1. Hozzon létre egy [Azure-fiókot](https://azure.microsoft.com/free/cognitive-services/). Ha már rendelkezik ilyennel, ugorjon a következő lépésre.
2. A kulcs és a végpont beszerzéséhez hozzon létre erőforrásokat a Computer Vision és az arc a Azure Portalban. Győződjön meg arról, hogy a telepítés során az ingyenes szintet (F0) választja.
   - [Computer Vision](https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)
   - [Arc](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) Az erőforrások üzembe helyezése után kattintson az **erőforrás keresése** elemre az egyes erőforrásokhoz tartozó kulcs és végpont összegyűjtéséhez. 
3. A [kognitív-Samples-VideoFrameAnalysis GitHub-](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) tárház klónozása.
4. Nyissa meg a mintát a Visual Studio 2015-es vagy újabb verziójával, majd hozza létre és futtassa a minta alkalmazásokat:
    - A BasicConsoleSample esetében a Face kulcs nem kódolható közvetlenül a [BasicConsoleSample/program. cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs)-ban.
    - A LiveCameraSample mezőben adja meg a kulcsokat az alkalmazás **Beállítások** ablaktábláján. A kulcsok a felhasználói adatként megmaradnak a munkamenetek között.

Ha készen áll a minták integrálására, a saját projektjeiből hivatkozhat a VideoFrameAnalyzer könyvtárra.

A VideoFrameAnalyzer-, hang-, videó-és szöveg-megértési képességei az Azure Cognitive Services-t használják. A Microsoft fogadja a képeket, a hang-és a videót, valamint a feltöltött egyéb adatok (az alkalmazáson keresztül), és felhasználhatja őket szolgáltatás-javító célokra. Szükségünk van az Ön közreműködésére az alkalmazás által az Azure Cognitive Services szolgáltatásba küldött adatok tulajdonosainak védelmében.

## <a name="summary"></a>Összegzés

Ebből a cikkből megtudhatta, hogyan futtathatja a közel valós idejű elemzést élő videós streameken az arc-és Computer Vision szolgáltatások használatával. Azt is megtanulta, hogyan használhatja a minta kódját a kezdéshez.

Nyugodtan visszajelzést és javaslatokat adhat a GitHub- [tárházban](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/). Az API-k szélesebb körű visszajelzésének megadásához keresse fel a [UserVoice webhelyét](https://cognitive.uservoice.com/).

