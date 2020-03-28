---
title: 'Példa: Valós idejű videóelemzés - Arc'
titleSuffix: Azure Cognitive Services
description: A Face szolgáltatás segítségével közel valós idejű elemzést végezhet az élő videóstreamből vett képkockákon.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: ab3f596000216e8555bb84d0d47aff9a6e969eeb
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169904"
---
# <a name="example-how-to-analyze-videos-in-real-time"></a>Példa: Videók valós idejű elemzése

Ez az útmutató azt ismerteti, hogyan végezhet közel valós idejű elemzést egy élő videó-adatfolyam képkockáin. Az ilyen rendszerek alapvető összetevői:

- A képkockák beszerzése a videóforrásból
- Az elemezni kívánt képkockák kiválasztása
- A képkockák elküldése az API-ra
- Az API-hívás által visszaadott elemzési eredmények feldolgozása

Ezek a minták C# nyelven vannak írva, és a [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/)kód megtalálható a GitHubon itt: .

## <a name="the-approach"></a>A módszer

A videostreamek közel valós idejű elemzése többféleképpen oldható meg. Itt most három módszert mutatunk be, növekvő kifinomultsági szinten.

### <a name="a-simple-approach"></a>Az egyszerű módszer

A közel valós idejű elemzőrendszer legegyszerűbb kialakítása egy végtelen ciklus, ahol minden iteráció megragad egy keretet, elemzi, majd felhasználja az eredményt:

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

Ha az elemzést egy kisméretű ügyféloldali algoritmus végzi, ez a módszer megfelelő. Azonban, ha elemzés történik a felhőben, a késés érintett azt jelenti, hogy egy API-hívás eltarthat néhány másodpercig. Ez idő alatt, nem vagyunk elfog képeket, és a téma lényegében nem csinál semmit. A maximális képkocka-sebességet így az API-hívások késése korlátozza.

### <a name="parallelizing-api-calls"></a>Az API-hívások párhuzamosítása

Bár a kisméretű ügyféloldali algoritmusokhoz megfelelő egy egyszerű egyszálas hurok, a felhőalapú API-hívásokhoz a felmerülő késés miatt nem a legmegfelelőbb. A megoldás erre a problémára az, hogy a hosszú futású API-hívásokat párhuzamosan hajtjuk végre a képkockák rögzítésével. C# esetén ezt elérhetnénk feladatalapú párhuzamossággal, például:

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

A kód mindegyik elemzést külön feladatban indítja el, amelyek a háttérben futnak, miközben újabb képkockákat rögzíthetünk. Ezzel a módszerrel elkerüljük a fő szál blokkolását, miközben egy API-hívás visszatérésére várunk, de elvesztettük a garanciák egy részét, amelyeket az egyszerű verzió biztosított. Több API-hívás is előfordulhat, hogy párhuzamosan, és az eredmények előfordulhat, hogy rossz sorrendben adják vissza. Így esetleg több szál egyszerre lép a ConsumeResult() függvénybe, ami veszélyes lehet, ha a függvény nem szálbiztos. Végezetül pedig ez az egyszerű kód nem követi nyomon a létrehozott feladatokat, így a kivételek csendesen eltűnnek. Ezért az utolsó lépés egy "fogyasztói" szál hozzáadása, amely nyomon követi az elemzési feladatokat, kivételeket vet fel, megöli a hosszú ideig futó feladatokat, és biztosítja, hogy az eredmények a megfelelő sorrendben legyenek felhasználva.

### <a name="a-producer-consumer-design"></a>A létrehozó-feldolgozó kialakítás

A végső "előállító-fogyasztó" alapú rendszerünkben van egy előállító szál, amely a korábbi végtelen ciklusunkhoz hasonlít. Ahelyett azonban, hogy az elemzési eredményeket azonnal feldolgozná, amint elérhetők, a feldolgozó egyszerűen egy üzenetsorba küldi a feladatokat, ahol nyomon követheti őket.

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

Van egy fogyasztói szálunk is, amely leveszi a feladatokat a várólistáról, megvárja, amíg befejeződnek, és vagy megjeleníti az eredményt, vagy felveti a kidobott kivételt. Az üzenetsor használatával biztosíthatjuk, hogy az eredmények egyesével, a megfelelő sorrendben lesznek feldolgozva, a rendszer maximális képkocka-sebességének korlátozása nélkül.

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

Ahhoz, hogy az alkalmazás a lehető leggyorsabban működjön, a fent leírt rendszer rugalmas megvalósítását fogja használni. A kód eléréséhez [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis)nyissa meg a at.

A kódtár tartalmazza a FrameGrabber osztályt, amellyel megvalósítható a fent leírt létrehozó-feldolgozó rendszer egy webkamera által szolgáltatott videoképkockák feldolgozására. A felhasználó megadhatja az API-hívás pontos formáját, és az osztály események segítségével tudatja a hívókóddal, ha új keretet szerez be, vagy új elemzési eredmény érhető el.

Az egyes lehetőségek bemutatására a kódtárat használó két mintaalkalmazás érhető el. Az első egy egyszerű konzolalkalmazás, és az alábbiakban egy egyszerűsített változata reprodukálható. Megragadja a képkockákat az alapértelmezett webkamerából, és elküldi őket a Face szolgáltatásba arcfelismerésre.

```csharp
using System;
using VideoFrameAnalyzer;
using Microsoft.ProjectOxford.Face;
using Microsoft.ProjectOxford.Face.Contract;
     
namespace VideoFrameConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            // Create grabber, with analysis type Face[]. 
            FrameGrabber<Face[]> grabber = new FrameGrabber<Face[]>();
            
            // Create Face Client. Insert your Face API key here.
            private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials("<subscription key>"),
            new System.Net.Http.DelegatingHandler[] { });

            // Set up our Face API call.
            grabber.AnalysisFunction = async frame => return await faceClient.DetectAsync(frame.Image.ToMemoryStream(".jpg"));

            // Set up a listener for when we receive a new result from an API call. 
            grabber.NewResultAvailable += (s, e) =>
            {
                if (e.Analysis != null)
                    Console.WriteLine("New result received for frame acquired at {0}. {1} faces detected", e.Frame.Metadata.Timestamp, e.Analysis.Length);
            };
            
            // Tell grabber to call the Face API every 3 seconds.
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running.
            grabber.StartProcessingCameraAsync().Wait();

            // Wait for keypress to stop
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();
            
            // Stop, blocking until done.
            grabber.StopProcessingAsync().Wait();
        }
    }
}
```

A második mintaalkalmazás egy kicsit érdekesebb, és ebben kiválasztható, hogy melyik API-t hívja meg a videóképeken. A bal oldalon az alkalmazás az élő videó előnézetét jeleníti meg, a jobb oldalon pedig a legújabb API-eredményéket jeleníti meg a megfelelő képkockára vetítve.

A legtöbb módban látható késés mutatkozik a bal oldali élő videókép és a jobb oldalon megjelenített eredmények közt. Ez a késés az API-kérés feldolgozásának időtartamával egyezik. Az egyik kivétel az "EmotionsWithClientFaceDetect" mód, amely az OpenCV használatával helyileg végzi el az arcfelismerést az ügyfélszámítógépen, mielőtt bármilyen lemezképet küldene a Cognitive Servicesszolgáltatásnak. Ily módon azonnal vizualizálhatjuk az észlelt arcot, majd frissíthetjük az érzelmeket, amint az API-hívás visszatér. Ez egy példa egy "hibrid" megközelítés, ahol az ügyfél végezhet néhány egyszerű feldolgozást, és a Cognitive Services API-k bővítheti ezt a fejlettebb elemzés, ha szükséges.

![HowToAnalyzeVideo](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>A kódbázisba való integráció

A minta használatához hajtsa végre az alábbi lépéseket:

1. A Vision API-k API-kulcsainak beszerzése az [előfizetésekből](https://azure.microsoft.com/try/cognitive-services/). A videoképkockák elemzéséhez megfelelő API-k:
    - [Computer Vision API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [Arcfelismerési API](https://docs.microsoft.com/azure/cognitive-services/face/overview)

2. Klónozza a [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) GitHub-adattárat

3. Nyissa meg a mintát a Visual Studio 2015-ben, és építse fel és futtassa a mintaalkalmazásokat:
    - A BasicConsoleSample esetében a Face billentyű közvetlenül a [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs)programban van kódolva.
    - A LiveCameraSample esetében a kulcsokat az alkalmazás Settings (Beállítások) panelén kell megadni. A kulcsokat a rendszer felhasználói adatként a munkamenetek közt is megőrzi.
        

Ha készen áll az integrációra, **hivatkozzon a VideoFrameAnalyzer könyvtárra a saját projektjeiből.** 

## <a name="summary"></a>Összefoglalás

Ebben az útmutatóban megtanulta, hogyan futtathat közel valós idejű elemzéseket élő videóstreameken az Arc, a Számítógépes látás és az Emotion API-k használatával, és hogyan használhatja a mintakódot az első lépésekhez. Az azure Cognitive Services regisztrációs oldalán megkezdheti az alkalmazás készítését az ingyenes [API-kulcsok](https://azure.microsoft.com/try/cognitive-services/)használatával. 

Nyugodtan adjon visszajelzést és javaslatokat a [GitHub-adattárban,](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) vagy szélesebb KÖRŰ API-visszajelzések esetén a [UserVoice webhelyen.](https://cognitive.uservoice.com/)

## <a name="related-topics"></a>Kapcsolódó témakörök
- [Hogyan lehet arcokat azonosítani a képen](HowtoIdentifyFacesinImage.md)
- [Hogyan lehet arcokat megtalálni a képen](HowtoDetectFacesinImage.md)
