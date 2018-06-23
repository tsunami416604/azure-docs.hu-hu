---
title: Valós idejű videó elemzés Arcfelismerési API-val |} Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Közel valós idejű elemzést származó élő video-adatfolyamot keretek használhat kognitív szolgáltatásokban a Arcfelismerési API-t.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 8675f992ddffe2eedfeac294a6c57560434802c2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347602"
---
# <a name="how-to-analyze-videos-in-real-time"></a>A valós idejű elemzése a videók
Ez az útmutató fog bemutatják, hogyan lehet egy élő video-adatfolyamot vett keretek közel valós idejű elemzést. Az ilyen rendszer alapvető összetevői a következők:
- Szerezzen be videó forrásból keretek
- Válassza ki a kereteket elemzése
- Küldje el e keretek az API-hoz
- Minden egyes visszaküldött eredményből felhasználása az API-hívás

Ezeket a mintákat a C# nyelven íródtak, és a kód a Githubon itt található: [ https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis ](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

## <a name="the-approach"></a>A módszer
Több módon is megoldható a közel valós idejű elemzési futó video-adatfolyamot. A Microsoft képzettsége szintű növekvő három módszer struktúrába rendezése indul el.

### <a name="a-simple-approach"></a>Egy egyszerű módszer
A legegyszerűbb kialakítása a közel valós idejű elemzési rendszer végtelen hurkot, ahol minden egyes ismétlés azt keret adása, elemzése, és felhasználni az eredmény esetén:
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
Ha az analysis egy egyszerűsített ügyféloldali algoritmus állt, ez a megközelítés megfelelő lesz. Azonban az elemzés a felhőben történik, ha a Tiltás késése azt jelenti, hogy egy API-hívás eltarthat néhány másodpercig, amely idő alatt azt nem lemezképek rögzítése és a szál lényegében tesz a semmi. A maximális képkockasebessége korlátozza az API-hívások késését.

### <a name="parallelizing-api-calls"></a>Parallelizing API-hívások
Egy egyszerű egyszálas hurok egy egyszerűsített ügyféloldali algoritmushoz szabálykészletében, amíg nem fér a késéssel jár felhő API-hívásokban is. A probléma megoldása érdekében a rendszer engedélyezi a hosszan futó API-hívások végrehajtása a keret grabbing párhuzamosan. C# nyelven íródtak azt el lehet érni, ez például feladatalapú párhuzamossági használata:
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
Ekkor elindul egy külön feladatban futtathatja a háttérben, miközben továbbra is új keretek grabbing elemzéseket. Ezzel elkerülhető, hogy blokkolja a fő szálnak-Várakozás egy API-hívás való visszatéréshez, azonban azt megszakadt néhány garanciákat, egyszerű verziója – több API-hívások előfordulhat, hogy párhuzamosan, és előfordulhat, hogy a megfelelő sorrendben adja vissza az eredményeket. Ez több szál ConsumeResult() függvény egyidejűleg, adja meg a legyen veszélyes, ha a függvény nem szálbiztos is okozhatja. Végül, ez a kód egyszerű nem nyomon követheti, létrehozása, feladatok, kivételek csendes eltűnik. Ebből kifolyólag a végső való hozzáadása összetevője, amely a feladatok elemzését követi, kivételek ablakába, kill hosszan futó feladatokat, és győződjön meg arról, az eredmények lekérése felhasznált egyenként, a megfelelő sorrendben "ügyfél" szál.

### <a name="a-producer-consumer-design"></a>A gyártó-fogyasztó kialakítása
A végső "készítőhöz-fogyasztó" rendszerben a gyártó szál nagyon hasonló a korábbi végtelen hurkot van. Azonban helyett fel az elemzés eredményeinek, amint érhetők el, a gyártó egyszerűen elhelyezi a feladatok várólista nyomon követéséhez őket.
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
Azt is egy fogyasztói szál vesz ki a várólistában, befejeződnek, várakozó feladatok és az eredmény megjelenítéséhez vagy a kivételküldésre lépett fel. A várólista használatával garantáljuk, hogy eredmények lekérése felhasznált egyszerre csak egy, a megfelelő sorrendben, a rendszer maximális képkockasebessége korlátozása nélkül.
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
Ahhoz, hogy az alkalmazás fel, és minél gyorsabban fut, a Microsoft alkalmaztunk a rendszer a fent említett szándékos volt úgy, hogy elég rugalmas, ugyanakkor nem könnyen használható számos forgatókönyv végrehajtásához. A kód eléréséhez lépjen [ https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis ](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis).

A könyvtár FrameGrabber, a gyártó-fogyasztó rendszer kapcsolatban a fentiekben ismertetett feldolgozni egy webkamera a videó keretek megvalósító osztályt tartalmazza. A felhasználó megadhatja az API-hívás pontos formájában, és az osztály eseményeket használ, hogy a hívó kód tudja, amikor egy új keret keletkezik, vagy egy új eredményből érhető el.

Bemutatják az egyes lehetőségek, nincsenek két mintaalkalmazások, hogy használja-e a tárat. Az első egy egyszerű Konzolalkalmazás, és ez egy egyszerűsített verziója alatt jelenik meg. Az alapértelmezett webkamera érkező keretek grabs, és elküldi azokat a Arcfelismerési API felület észleléséhez.
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
A második mintaalkalmazás érdekesebb egy kicsit, és lehetővé teszi annak meghatározását, mely API-hívás a videó keretek. A bal oldali az alkalmazás jeleníti meg az élő videót, a jobb oldali előnézete azt mutatja, hogy a legújabb API-eredményéhez a megfelelő keret átfedett.

Legtöbbször lesz látható késleltetés között az élő felvétel a bal oldali és a jobb oldali feladatkonfigurációkat elemzése. Ez a késés az API-hívás végrehajtásához szükséges idő. A kivétel ez alól a "EmotionsWithClientFaceDetect" módban van, amely arcfelismerési észlelési helyileg végrehajtja az ügyfélszámítógépen, OpenCV, használó összes lemezképet kognitív szolgáltatásokhoz való elküldése előtt. Ezzel azt is azonnal jelenítheti meg az észlelt tapasztalt, és frissítse a érzelmek később vissza az API hívása után. Ez azt mutatja be a "hibrid" megközelítést, ha néhány egyszerű feldolgozás az ügyfélen hajtható végre, és majd kognitív szolgáltatások API-k segítségével kiegészítheti a speciális elemzés, amikor erre szükség van a lehetőségét.

![HowToAnalyzeVideo](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>A codebase integrálása
Bevezetés a minta használatába, kövesse az alábbi lépéseket:

1. API-kulcsok beszerzése a Látástechnológiai API- [előfizetések](https://azure.microsoft.com/try/cognitive-services/). Videó keret elemzése a megfelelő API-kat a következők:
    - [Computer Vision API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [Érzelemfelismerési API](https://docs.microsoft.com/azure/cognitive-services/emotion/home)
    - [Face API](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. Klónozott a [Cognitive-minták-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) GitHub-tárház

3. Nyissa meg a minta a Visual Studio 2015, felépítéséhez, valamint a mintaalkalmazások futtatása:
    - A BasicConsoleSample, Arcfelismerési API-kulcsot nem változtatható közvetlenül a [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - LiveCameraSample a kulcsok kell adni az alkalmazás a beállítások ablaktáblára. Ezek maradnak felhasználói adatként-munkamenetek között.
        

Amikor készen áll a integrálása, **egyszerűen a saját projektek használatával hivatkozik a VideoFrameAnalyzer könyvtárban.** 



## <a name="developer-code-of-conduct"></a>Fejlesztői viselkedési szabályzat
Minden kognitív szolgáltatásokkal, az API-k és minták fejlesztése fejlesztők szükséges kövesse a "[fejlesztői etikai kódex Microsoft Cognitive szolgáltatások](https://azure.microsoft.com/support/legal/developer-code-of-conduct/)." 


A kép, hang-, videó vagy szöveges VideoFrameAnalyzer lehetőségeinek ismertetése Microsoft kognitív szolgáltatásokat használ. A Microsoft fog kapni a képek, hang, video-, és egyéb adatokat, hogy feltöltése (keresztül ezt az alkalmazást), és előfordulhat, hogy használjon szolgáltatás javítása céljából. A személyeket, amelynek adatait az alkalmazás küld a Microsoft kognitív szolgáltatások védelme a segítségét kérjük. 


## <a name="summary"></a>Összegzés
Ebben az útmutatóban megtanulta, hogyan használhatja a mintakódot a kezdéshez, és közel valós idejű elemzési futtatásáról a oldallal, számítógép stratégiai és Érzelemfelismerési API-k használatával élő video-adatfolyamot.  Ismerkedés az alkalmazás elkészítése az ingyenes API-kulcsokat, a [Microsoft kognitív szolgáltatások regisztrációs oldalon](https://azure.microsoft.com/try/cognitive-services/). 

Küldje el nyugodtan visszajelzését és javaslatait az biztosításához a [GitHub-tárházban](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/), vagy további széleskörű API visszajelzés a a [UserVoice webhelyén](https://cognitive.uservoice.com/).



## <a name="related"></a> Kapcsolódó témakörök
- [A kép néz azonosítása](HowtoIdentifyFacesinImage.md)
- [A kép néz történő Adatmásolást](HowtoDetectFacesinImage.md)
