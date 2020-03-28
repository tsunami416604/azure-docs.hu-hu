---
title: Videók elemzése közel valós időben - Computer Vision
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan végezhet közel valós idejű elemzést olyan képkockákon, amelyek élő videofolyamból származnak a Computer Vision API használatával.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 18b158b7a4881619b93ab404de67f7bb25f92b6a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76166826"
---
# <a name="analyze-videos-in-near-real-time"></a>Videók elemzése közel valós időben

Ez a cikk bemutatja, hogyan végezhet közel valós idejű elemzést olyan képkockákon, amelyek élő videostreamből származnak a Computer Vision API használatával. Az ilyen elemzés alapvető elemei a következők:

- Képkockák beolvasása videoforrásból.
- Az elemzendő keretek kiválasztása.
- A keretek elküldése az API-ba.
- Az API-hívásból visszaadott elemzési eredmény elfogyasztása.

Az ebben a cikkben szereplő minták C#-ban vannak megírva. A kód eléréséhez nyissa meg a [GitHub Videoframe analysis mintalapját.](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/)

## <a name="approaches-to-running-near-real-time-analysis"></a>Megközelítések a közel valós idejű elemzés futtatásához

Megoldhatja a problémát a futó közel valós idejű elemzés videó stream segítségével a különböző megközelítések. Ez a cikk felvázolja három közülük, a növekvő szintű kifinomultság.

### <a name="design-an-infinite-loop"></a>Végtelen ciklus tervezése

A legegyszerűbb design közel valós idejű elemzés egy végtelen ciklus. Ennek a huroknak minden ismétlésében megragad egy keretet, elemzi, majd felhasználja az eredményt:

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

Ha az elemzés egy könnyű, ügyféloldali algoritmusból állna, ez a megközelítés megfelelő lenne. Azonban, ha az elemzés történik a felhőben, az eredményül kapott késés azt jelenti, hogy egy API-hívás eltarthat néhány másodpercig. Ez idő alatt, te nem elfog képeket, és a téma lényegében nem csinál semmit. A maximális képkockasebességet az API-hívások késése korlátozza.

### <a name="allow-the-api-calls-to-run-in-parallel"></a>Az API-hívások párhuzamos futtatásának engedélyezése

Bár egy egyszerű, egyszálas hurok van értelme egy könnyű, ügyféloldali algoritmus, nem illeszkedik jól a késés egy felhőalapú API-hívás. A megoldás erre a problémára az, hogy a hosszú ideig futó API-hívás párhuzamosan fut a keret-rángatás. A C#-ban ezt feladatalapú párhuzamosság használatával teheti meg. Futtathatja például a következő kódot:

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

Ezzel a módszerrel minden elemzést külön feladatban indíthat el. A feladat a háttérben is futtatható, miközben folytatja az új képkockák megragadását. A megközelítés elkerüli a fő szál blokkolását, miközben egy API-hívás visszatérésére vár. A megközelítés azonban bizonyos hátrányokat jelenthet:
* Ez költségek ön néhány garancia, hogy az egyszerű változat biztosított. Ez azt, hogy több API-hívás fordulhat elő párhuzamosan, és az eredmények előfordulhat, hogy rossz sorrendben adják vissza. 
* Azt is okozhatja, hogy több szál egyszerre adja meg a ConsumeResult() függvényt, ami veszélyes lehet, ha a függvény nem szálbiztos. 
* Végül ez az egyszerű kód nem követi nyomon a létrehozott feladatokat, így a kivételek csendben eltűnnek. Így hozzá kell adnia egy "fogyasztói" szálat, amely nyomon követi az elemzési feladatokat, kivételeket vet fel, megöli a hosszú ideig futó feladatokat, és biztosítja, hogy az eredmények a megfelelő sorrendben, egyenként legyenek.

### <a name="design-a-producer-consumer-system"></a>Termelő-fogyasztói rendszer kialakítása

A végső megközelítés, tervezése "gyártó-fogyasztó" rendszer, akkor létre egy termelő szál, amely hasonlít a korábban említett végtelen hurok. Azonban ahelyett, hogy az elemzési eredményeket a mint azok rendelkezésre állnak, a gyártó egyszerűen elhelyezi a feladatokat egy sorban, hogy nyomon kövesse őket.

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

Hozzon létre egy fogyasztói szálat is, amely leveszi a feladatokat a várólistáról, megvárja, amíg befejeződnek, és vagy megjeleníti az eredményt, vagy felveti a kidobott kivételt. A várólista használatával garantálhatja, hogy az eredmények egyenként, a megfelelő sorrendben kerülnek felhasználásra, a rendszer maximális képkockasebességének korlátozása nélkül.

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

## <a name="implement-the-solution"></a>A megoldás megvalósítása

### <a name="get-started-quickly"></a>Első lépések gyorsan

Az alkalmazás lehető leggyorsabb működésének elősegítése érdekében megvalósítottuk az előző szakaszban ismertetett rendszert. Célja, hogy elég rugalmas legyen ahhoz, hogy számos forgatókönyvet befogadjon, miközben könnyen használható. A kód eléréséhez nyissa meg a [GitHub Videoframe analysis mintalapját.](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/)

A könyvtár `FrameGrabber` tartalmazza az osztályt, amely a korábban tárgyalt gyártó-fogyasztói rendszert valósítja meg a videokeretek feldolgozásához egy webkameráról. A felhasználók megadhatják az API-hívás pontos formáját, és az osztály események segítségével tudathatja a hívókóddal, ha új keretet szerzett be, vagy ha új elemzési eredmény érhető el.

A lehetőségek egy részének szemléltetése érdekében két mintaalkalmazást biztosítottunk, amelyek a könyvtárat használják. 

Az első mintaalkalmazás egy egyszerű konzolalkalmazás, amely megragadja a kereteket az alapértelmezett webkameráról, majd elküldi őket a Face szolgáltatásba arcfelismerésre. Az alkalmazás egyszerűsített verziója a következő kódban jelenik meg:

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

A második minta alkalmazás egy kicsit érdekesebb. Ez lehetővé teszi, hogy melyik API-t hívja a videokeretek. A bal oldalon az alkalmazás megjeleníti az élő videó előnézetét. A jobb oldalon a megfelelő keretlegutóbbi API-eredményt fedi le.

A legtöbb módban látható késleltetés van a bal oldali élő videó és a jobb oldali láthatóvá vált elemzés között. Ez a késleltetés az AZ IDŐ, amely az API-hívás hoz. Kivételt képez az "EmotionsWithClientFaceDetect" módban, amely elvégzi az arcfelismerés helyileg az ügyfélszámítógépen az OpenCV használatával, mielőtt bármilyen lemezképet küld az Azure Cognitive Services. 

Ezzel a módszerrel azonnal vizualizálhatja az észlelt arcot. Ezután frissítheti az érzelmeket később, miután az API-hívás visszatér. Ez a "hibrid" megközelítés lehetőségét mutatja. Ez azt, hogy néhány egyszerű feldolgozás elvégezhető az ügyfélen, majd a Cognitive Services API-k at lehet használni, hogy fokozza ezt a feldolgozást a fejlettebb elemzés, ha szükséges.

![A LiveCameraSample alkalmazás címkékkel ellátott képet jelenít meg](../../Video/Images/FramebyFrame.jpg)

### <a name="integrate-the-samples-into-your-codebase"></a>A minták integrálása a kódbázisba

A minta első lépései:

1. A Vision API-k API-kulcsainak beszerzése az [előfizetésekből](https://azure.microsoft.com/try/cognitive-services/). A videokeret-elemzés hez a vonatkozó szolgáltatások a következők:
    - [Számítógépes látás](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [Arcfelismerés](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. Klónozza a [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) GitHub-tárta.

3. Nyissa meg a mintát a Visual Studio 2015-ben vagy újabb verzióban, majd építse fel és futtassa a mintaalkalmazásokat:
    - A BasicConsoleSample esetében a Face billentyű közvetlenül a [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs)programban van kódolva.
    - A LiveCameraSample mezőbe írja be a kulcsokat az alkalmazás **Beállítások** ablaktáblájában. A kulcsok felhasználói adatként a munkamenetek között megmaradnak.

Ha készen áll a minták integrálására, hivatkozzon a VideoFrameAnalyzer könyvtárra a saját projektjeiből.

A VideoFrameAnalyzer kép-, hang-, video- és szövegismereti képességei az Azure Cognitive Services-t használják. A Microsoft megkapja az Ön által feltöltött képeket, hang-, video- és egyéb adatokat (ezen az alkalmazáson keresztül), és felhasználhatja azokat szolgáltatásfejlesztési célokra. Szükségünk van az Ön közreműködésére az alkalmazás által az Azure Cognitive Services szolgáltatásba küldött adatok tulajdonosainak védelmében.

## <a name="summary"></a>Összefoglalás

Ebben a cikkben megtanulta, hogyan futtathat közel valós idejű elemzést élő videóstreameken az Arc- és Computer Vision szolgáltatások használatával. Azt is megtanulta, hogyan használhatja a mintakódot a kezdéshez. Az alkalmazás ingyenes API-kulcsok használatával történő létrehozásának megkezdéséhez nyissa meg az [Azure Cognitive Services regisztrációs lapját.](https://azure.microsoft.com/try/cognitive-services/)

Nyugodtan adjon visszajelzést és javaslatokat a [GitHub-tárházban.](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) A szélesebb API-visszajelzéshez látogasson el [uservoice webhelyünkre.](https://cognitive.uservoice.com/)

