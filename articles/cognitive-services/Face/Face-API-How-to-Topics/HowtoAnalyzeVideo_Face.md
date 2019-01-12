---
title: 'Példa: Valós idejű videóelemzés – Face API'
titleSuffix: Azure Cognitive Services
description: A Face API segítségével közel valós idejű elemzést végezhet az élő videó-adatfolyamból vett képkockákon.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 6aac7d99e002413406022388eaa7ad1a98ae7b34
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2019
ms.locfileid: "54232158"
---
# <a name="example-how-to-analyze-videos-in-real-time"></a>Példa: Videók valós idejű elemzése

Ez az útmutató azt ismerteti, hogyan végezhet közel valós idejű elemzést egy élő videó-adatfolyam képkockáin. Az ilyen rendszerek alapvető összetevői:

- A képkockák beszerzése a videóforrásból
- Az elemezni kívánt képkockák kiválasztása
- A képkockák elküldése az API-ra
- Az API-hívás által visszaadott elemzési eredmények feldolgozása

A kódminták C# nyelven íródtak, a kód a GitHubon a következő címen található: [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

## <a name="the-approach"></a>A módszer

A videostreamek közel valós idejű elemzése többféleképpen oldható meg. Itt most három módszert mutatunk be, növekvő kifinomultsági szinten.

### <a name="a-simple-approach"></a>Az egyszerű módszer

Közel valós idejű elemzési rendszer esetén a legegyszerűbb kialakítás végtelen ciklust, ahol minden egyes ismétléskor begyűjti a keret, elemzi azokat, és majd felhasználja az eredmény:

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

Ha az elemzést egy kisméretű ügyféloldali algoritmus végzi, ez a módszer megfelelő. Azonban ha elemzés a felhőben történik, a késéssel érintett azt jelenti, hogy egy API-hívás eltarthat néhány másodpercig. Ebben az időszakban azt nem lemezképek rögzítése és a szál művelet lényegében állapotát. A maximális képkocka-sebességet így az API-hívások késése korlátozza.

### <a name="parallelizing-api-calls"></a>Az API-hívások párhuzamosítása

Bár a kisméretű ügyféloldali algoritmusokhoz megfelelő egy egyszerű egyszálas hurok, a felhőalapú API-hívásokhoz a felmerülő késés miatt nem a legmegfelelőbb. A megoldás erre a problémára az, hogy a hosszú futású API-hívásokat párhuzamosan hajtjuk végre a képkockák rögzítésével. A C# nyelvben ezt feladatalapú párhuzamosítással érhetjük el. Például a következőképp:

```CSharp
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

A kód mindegyik elemzést külön feladatban indítja el, amelyek a háttérben futnak, miközben újabb képkockákat rögzíthetünk. Ezzel a módszerrel azt elkerülése érdekében a fő szál blokkolása az egy API-hívások visszatérhet várakozás közben, de azt a garantálja, hogy a megadott egyszerű verzió némelyike megszakadt. Több API-hívások párhuzamosan is felléphet, és az eredmények lehet, hogy adja vissza a rendszer a nem megfelelő sorrendben. Ez több szálon egyszerre, adja meg a ConsumeResult() függvény, amely lehet veszélyes, ha a függvény nem szálbiztos is okozhatja. Végezetül pedig ez az egyszerű kód nem követi nyomon a létrehozott feladatokat, így a kivételek csendesen eltűnnek. Ezért az utolsó lépés, hogy adjon hozzá egy "felhasználói" hozzászóláslánc, amely az elemzési feladatokat követi, kivételek előléptetése, kill hosszan futó feladatokat, és győződjön meg arról, hogy az eredmények a megfelelő sorrendben felhasználása.

### <a name="a-producer-consumer-design"></a>A létrehozó-feldolgozó kialakítás

A végső "előállító-fogyasztó" alapú rendszerünkben van egy előállító szál, amely a korábbi végtelen ciklusunkhoz hasonlít. Ahelyett azonban, hogy az elemzési eredményeket azonnal feldolgozná, amint elérhetők, a feldolgozó egyszerűen egy üzenetsorba küldi a feladatokat, ahol nyomon követheti őket.

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

Azt is, amely a feladatok a várólista ki tart, megvárja, amíg befejeződnek, és mindkét jeleníti meg az eredményt vagy keletkezett kivételek kiváltja fogyasztói szálat. Az üzenetsor használatával biztosíthatjuk, hogy az eredmények egyesével, a megfelelő sorrendben lesznek feldolgozva, a rendszer maximális képkocka-sebességének korlátozása nélkül.

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

Az első alkalmazásához felfelé és a lehető leggyorsabban fut, a rendszer a fent leírt rugalmas megvalósítását fogja használni. A kódot a következő helyen éri el: [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis).

A tár FrameGrabber, és a egy webkamera előképe videókban feldolgozni a fent ismertetett előállítói – fogyasztói rendszer, amely osztályt tartalmazza. A felhasználó megadhatja az API-hívás pontos formája, és az osztály események használja ahhoz, hogy a hívó kód tudja, hogy amikor egy új keret igényelve, vagy egy új elemzési eredmény érhető el.

Az egyes lehetőségek bemutatására a kódtárat használó két mintaalkalmazás érhető el. Az első egy egyszerű konzolalkalmazást, és a egy egyszerűsített változata az alábbiakban találja. Az alkalmazás begyűjti a képkockákat az alapértelmezett webkameráról, és továbbítja azokat a Face API-ra arcfelismerésre.

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
            FaceServiceClient faceClient = new FaceServiceClient("<Subscription Key>");

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

A legtöbb módban látható késés mutatkozik a bal oldali élő videókép és a jobb oldalon megjelenített eredmények közt. Ez a késés az API-kérés feldolgozásának időtartamával egyezik. Kivételt jelent a "EmotionsWithClientFaceDetect" üzemmódját, amely arcfelismerés helyileg az ügyfélszámítógépen, a Cognitive Services összes lemezképet elküldése előtt OpenCV, használatával hajtja végre. Ezzel a módszerrel lehet a felismert arc azonnal megjelenítheti és frissítse az érzelmek, miután az API-hívás adja vissza. Ez a példa a "hibrid" megközelítést, ahol az ügyfél hajthat végre egyszerű feldolgozást, és a Cognitive Services API-k is bővítésével Ez a fejlett elemzési szükség esetén.

![HowToAnalyzeVideo](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>A kódbázisba való integráció

A minta használatához hajtsa végre az alábbi lépéseket:

1. A Vision API-k API-kulcsainak beszerzése az [előfizetésekből](https://azure.microsoft.com/try/cognitive-services/). A videoképkockák elemzéséhez megfelelő API-k:
    - [Computer Vision API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [Emotion API](https://docs.microsoft.com/azure/cognitive-services/emotion/home)
    - [Face API](https://docs.microsoft.com/azure/cognitive-services/face/overview)

2. Klónozza a [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) GitHub-adattárat

3. Nyissa meg a mintát a Visual Studio 2015-öt, és hozhat létre, és a mintaalkalmazások futtatása:
    - A BasicConsoleSample, a Face API-kulcs nem változtatható közvetlenül a [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - A LiveCameraSample esetében a kulcsokat az alkalmazás Settings (Beállítások) panelén kell megadni. A kulcsokat a rendszer felhasználói adatként a munkamenetek közt is megőrzi.
        

Ha készen áll, integrálni **hivatkozhat a saját projektek a VideoFrameAnalyzer könyvtárban.** 

## <a name="summary"></a>Összegzés

Ebben az útmutatóban megismerhette, közel valós idejű elemzés futtatása az élő video-adatfolyamokat a Face, a Computer Vision és az Emotion API-k használatával, és hogyan használható a mintakód a kezdéshez. Kiindulópontként használhat az alkalmazás ingyenes API-val, a kulcsok a [Azure Cognitive Services-előfizetés lapján](https://azure.microsoft.com/try/cognitive-services/). 

Nyugodtan visszajelzést és javaslatok a [GitHub-adattár](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) vagy szélesebb körű API-visszajelzés a a [UserVoice webhelyén](https://cognitive.uservoice.com/).

## <a name="related-topics"></a>Kapcsolódó témakörök
- [Hogyan lehet arcokat azonosítani a képen](HowtoIdentifyFacesinImage.md)
- [Hogyan lehet arcokat megtalálni a képen](HowtoDetectFacesinImage.md)
