---
title: 'Példa: Valós idejű videoelemzés – Emotion API'
titlesuffix: Azure Cognitive Services
description: Az Emotion API segítségével élő videostreamekből származó képkockák közel valós idejű elemzését végezheti.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: sample
ms.date: 01/25/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: df955a23393c82565e8f31e59e148798a0f89bbf
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48236480"
---
# <a name="example-how-to-analyze-videos-in-real-time"></a>Példa: Videók valós idejű elemzése

> [!IMPORTANT]
> Az Emotion API 2019. február 15-ével elavulttá válik. Az érzelemfelismerési képesség mostantól általánosan elérhető a [Face API](https://docs.microsoft.com/azure/cognitive-services/face/) részeként.

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
```CSharp
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
Bár a kisméretű ügyféloldali algoritmusokhoz megfelelő egy egyszerű egyszálas hurok, a felhőalapú API-hívásokhoz a felmerülő késés miatt nem a legmegfelelőbb. A megoldás erre a problémára az, hogy a hosszú futású API-hívásokat párhuzamosan hajtjuk végre a képkockák rögzítésével. A C# nyelvben ezt feladatalapú párhuzamosítással érhetjük el. Például a következőképp:

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

A kód mindegyik elemzést külön feladatban indítja el, amelyek a háttérben futnak, miközben újabb képkockákat rögzíthetünk. Ezzel elkerülhető a fő szál blokkolása, miközben az API-hívás eredményére várunk, azonban az egyszerű módszer által biztosított garanciák némelyikét elveszítjük: egyszerre párhuzamosan több API-hívás is lehet folyamatban, és az eredmények esetleg nem a megfelelő sorrendben érkeznek vissza. Így esetleg több szál egyszerre lép a ConsumeResult() függvénybe, ami veszélyes lehet, ha a függvény nem szálbiztos. Végezetül pedig ez az egyszerű kód nem követi nyomon a létrehozott feladatokat, így a kivételek csendesen eltűnnek. Ezért utolsó összetevőként hozzá kell adnunk egy „feldolgozó” szálat, amely nyomon követi az elemzési feladatokat, kezeli a kivételeket, lelövi a hosszan futó feladatokat, és biztosítja, hogy az eredmények a megfelelő sorrendben, egyenként legyenek feldolgozva.

### <a name="a-producer-consumer-design"></a>A létrehozó-feldolgozó kialakítás
Ebben az utolsó „létrehozó-feldolgozó” rendszerben rendelkezünk egy létrehozó szállal, amely nagyon hasonló az előző végtelen hurokhoz. Ahelyett azonban, hogy az elemzési eredményeket azonnal feldolgozná, amint elérhetők, a feldolgozó egyszerűen egy üzenetsorba küldi a feladatokat, ahol nyomon követheti őket.
```CSharp
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
```CSharp
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
```CSharp
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

            // Create Face API Client. Insert your Face API key here.
            FaceServiceClient faceClient = new FaceServiceClient("<subscription key>");

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

A legtöbb módban látható késés mutatkozik a bal oldali élő videókép és a jobb oldalon megjelenített eredmények közt. Ez a késés az API-kérés feldolgozásának időtartamával egyezik. Ez alól kivételt az „EmotionsWithClientFaceDetect” mód képez, amely helyileg is végez arcfelismerést az ügyfélszámítógépen az OpenCV használatával, mielőtt beküldené a képeket a Cognitive Services szolgáltatásnak. Ezáltal azonnal megjeleníthető a felismert arc, majd az érzelmekkel később kiegészíthető, ha megérkezik az API-hívás eredménye. Ez az alkalmazás a „hibrid” megközelítés lehetőségét mutatja, ahol az egyszerű feldolgozási feladatok elvégezhetők az ügyfélen, majd a Cognitive Services API-k használatával ez szükség szerint kiegészíthető további speciális elemzési műveletekkel.

![HowToAnalyzeVideo](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>A kódbázisba való integráció
A minta használatához hajtsa végre az alábbi lépéseket:

1. A Vision API-k API-kulcsainak beszerzése az [előfizetésekből](https://azure.microsoft.com/try/cognitive-services/). A videoképkockák elemzéséhez megfelelő API-k:
    - [Computer Vision API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [Emotion API](https://docs.microsoft.com/azure/cognitive-services/emotion/home)
    - [Face API](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. Klónozza a [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) GitHub-adattárat

3. Nyissa meg a mintát a Visual Studio 2015-ben, majd buildelje és futtassa a mintaalkalmazásokat:
    - A BasicConsoleSample esetében a Face API-kulcs nem változtatható módon rögzítve van közvetlenül a [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs) fájlban.
    - A LiveCameraSample esetében a kulcsokat az alkalmazás Settings (Beállítások) panelén kell megadni. A kulcsokat a rendszer felhasználói adatként a munkamenetek közt is megőrzi.


Amikor készen áll az integrációra, **egyszerűen hivatkozzon a VideoFrameAnalyzer kódtárra a projektjeiben.**



## <a name="developer-code-of-conduct"></a>Fejlesztői magatartási kódex
Ahogy minden Cognitive Services szolgáltatás esetében, az API-kat alkalmazó fejlesztőknek tartaniuk kell magukat [az Azure Cognitive Services fejlesztőire vonatkozó magatartási kódexben](https://azure.microsoft.com/support/legal/developer-code-of-conduct/) foglaltakhoz.


A VideoFrameAnalyzer kép-, hang-, videó- és szövegfelismerési képességei az Azure Cognitive Servicesen alapulnak. A feltöltött képeket, hangokat, videókat és egyéb adatokat a Microsoft fogadja (az alkalmazáson keresztül), és a szolgáltatás fejlesztése céljával azokat felhasználhatja. Szükségünk van az Ön közreműködésére az alkalmazás által az Azure Cognitive Services szolgáltatásba küldött adatok tulajdonosainak védelmében.


## <a name="summary"></a>Összegzés
Ebben az útmutatóban megismerhette, hogyan futtathat közel valós idejű elemzéseket élő videostreameken a Face, a Computer Vision és az Emotion API-k használatával, és hogy mindennek hogyan láthat neki a mintakód használatával.  Az alkalmazások összeállításának megkezdéséhez [az Azure Cognitive Services regisztrációs oldalán](https://azure.microsoft.com/try/cognitive-services/) talál ingyenes API-kulcsokat.

Visszajelzéseket és javaslatokat a [GitHub-adattárban](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/), vagy az API-kkal kapcsolatos általánosabb visszajelzések esetén a [UserVoice webhelyén](https://cognitive.uservoice.com/) adhat.
