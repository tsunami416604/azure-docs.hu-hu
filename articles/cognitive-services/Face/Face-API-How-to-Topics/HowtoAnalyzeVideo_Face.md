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
ms.openlocfilehash: 007b35c1338f2837187ae55817bf815072f6f0c7
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46127261"
---
# <a name="example-how-to-analyze-videos-in-real-time"></a>Példa: Videók valós idejű elemzése

Ez az útmutató azt ismerteti, hogyan végezhet közel valós idejű elemzést egy élő videó-adatfolyam képkockáin. Egy ilyen rendszer alapvető összetevői a következők:

- A képkockák kinyerése a videó forrásból
- Az elemezni kívánt képkockák kiválasztása
- A képkockák elküldése az API-nak
- Az API-hívás összes visszaadott elemzési eredményének feldolgozása

Ezek a minták a C# nyelven íródtak, és a kódjuk a következő GitHub-címen érhető el: [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

## <a name="the-approach"></a>A módszer

Többféle módon is meg lehet közelíteni a videó-adatfolyamok közel valós idejű elemzésének problémáját. Három különböző megközelítést mutatunk be az egyszerűbbtől az összetettebbekig.

### <a name="a-simple-approach"></a>Az egyszerű megközelítés

A valós-idejű elemzési rendszer legegyszerűbb megvalósítása, egy végtelen ciklus használata, amelyben minden iterációnál kiragadunk egy képkockát, elemezzük, majd feldolgozzuk az eredményt:

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

Ha az elemzést egy kis méretű ügyféloldali algoritmus végzi, akkor ez a megoldás megfelelő is lehet. Azonban, ha az elemzés a felhőben történik, a hálózati késés miatt egy-egy API-hívás másodpercekig is tarthat, amely idő alatt nem rögzítünk új képkockákat, és a végrehajtási szálunk lényegében tétlen. A maximális képkockasebességet így az API-hívások okozta késés korlátozza.

### <a name="parallelizing-api-calls"></a>API-hívások párhuzamos futtatása

Míg egy egyszerű egyszálas ciklus jó lehet egy könnyű ügyféloldali algoritmushoz, a felhőalapú API-hívásoknál a fennálló késés miatt nem igazán felel meg. A megoldás erre a problémára az, hogy a hosszú futású API-hívások végrehajtását a képkocka lekéréssel párhuzamosan engedjük végezni. C# esetén ezt elérhetnénk feladatalapú párhuzamossággal, például:

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

Ez minden elemzést külön feladatként indít, ami nyugodtan futhat a háttérben, amíg továbbra is gyűjtjük a képkockákat. Ezzel elkerülhető a fő szál blokkolása az API-hívás eredményére várakozáskor, ugyanakkor elveszítettünk az egyszerű verzió által nyújtott néhány garanciát: több API-hívás történhet párhuzamosan és az eredményeket nem biztos, hogy jó sorrendben kapjuk vissza. Ilyenkor az is megtörténhet, hogy több szál lép be a ConsumeResult() függvénybe egyidejűleg, ami veszélyes, ha a függvény nem szál-biztos. Végül, ez a kód nem követi nyomon a létrehozott feladatokat, így a kivételek csendben eltűnnek. Ezért az utolsó összetevő, amit hozzá kell adnunk, az a „fogyasztó” szál, ami nyomon követi az elemzést, elkapja a kivételeket, kilövi a hosszú ideig futó feladatokat, és biztosítja, hogy az eredmények feldolgozása jó sorrendben történjen, egyszerre mindig egy.

### <a name="a-producer-consumer-design"></a>A termelő-fogyasztó alapú tervezés

A végső „termelő – fogyasztó” rendszerünkben van egy termelő szálunk, ami nagyon hasonlít az előző végtelen ciklusunkhoz. Azonban ahelyett, hogy az érkező elemzési eredményeket azonnal feldogozná, a termelő a nyomon követéshez egyszerűen egy sorba rakja a feladatokat.

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

Van egy fogyasztói szálunk is, ami a sorból veszi ki a feladatokat, megvárja amíg befejeződnek, majd vagy megjeleníti az eredményeket vagy elkapja a dobott kivételt. A sor használatával garantálni tudjuk, hogy az eredmények feldolgozására egyesével kerüljön sor, a jó sorrendben és anélkül, hogy a rendszer maximális képkocka-sebességét korlátoznánk.

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

Ahhoz, hogy az alkalmazás a lehető leghamarabb elkészüljön és futtatni lehessen, megvalósítottuk a fent leírt rendszert azzal a céllal, hogy elég rugalmas legyen többféle forgatókönyv megvalósításához, de könnyen használható legyen. A kód itt érhető el: [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis).

A könyvtár tartalmazza a FrameGrabber osztályt, ami a webkameráról érkező videó képkockák feldolgozására a fent tárgyalt termelő-fogyasztó rendszert valósítja meg. A felhasználó megadhatja az API-hívás pontos formáját, az osztály események használatával tudatja a hívó kóddal, amikor új képkockára tett szert vagy amikor elkészült egy elemzési eredmény.

A lehetőségek bemutatására két a könyvtárat használó mintaalkalmazás van. Az első egy egyszerű konzol alkalmazás, aminek egyszerűsített változatát alább megadjuk. Az alapértelmezett webkameráról veszi a képkockákat és elküldi őket arcfelismerésre a Face API-nak.

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

A második mintaalkalmazás kicsit érdekesebb, választani lehet, hogy melyik API-t hívjuk meg a videó képkockára. A bal oldalon az alkalmazás az élő videó előnézetét jeleníti meg, a jobb oldalon a legújabb API-eredmény látható a megfelelő képkockára fektetve.

A legtöbb módban látható késés lesz a bal oldali élő videó és a jobb oldali szemléltetett elemzés között. Ez a késés az API-hívás végrehajtásának ideje. Kivétel ez alól a „EmotionsWithClientFaceDetect” módban van, amely helyileg, az ügyfélszámítógépen az OpenCV használatával arcfelismerést végez, mielőtt képeket küldene a Cognitive Services számára. Ily módon a felismert arcot azonnal megjeleníthetjük, és később frissítjük az érzelemmel, miután az API-hívás elkészült. Ez „hibrid” megközelítés lehetőségét mutatja, ahol néhány egyszerű feldolgozást az ügyfél is elvégezhet, majd szükség szerint a Cognitive Services API-k fejlettebb elemzéseinek használatával tovább finomítható.

![HowToAnalyzeVideo](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>Beépítés a kódbázisba

A minta használatának megkezdéséhez kövesse a következő lépéseket:

1. Kérjen API-kulcsokat a Vision API-khoz az [Előfizetéseknél](https://azure.microsoft.com/try/cognitive-services/). Videó képkocka elemzéshez megfelelő API-k a következők:
    - [Computer Vision API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [Emotion API](https://docs.microsoft.com/azure/cognitive-services/emotion/home)
    - [Face API](https://docs.microsoft.com/azure/cognitive-services/face/overview)

2. A [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) GitHub-adattár klónozása

3. Nyissa meg a mintát a Visual Studio 2015-ben, build után futtassa a mintaalkalmazásokat:
    - A BasicConsoleSample esetén a Face API-kulcs nem változtatható, közvetlenül a [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs) fájlban van.
    - A LiveCameraSample esetén a kulcsokat az alkalmazás beállítások panelén kell megadni. Munkamenetek között megőrződnek mint felhasználói adat.
        

Ha készen áll a beépítésre, **egyszerűen hivatkozzon a saját projektből a VideoFrameAnalyzer könyvtárra.** 



## <a name="developer-code-of-conduct"></a>Fejlesztői magatartási kódex

Mint valamennyi Cognitive Services esetén az API-jainkat használó fejlesztőknek is követniük kell a „[Microsoft Cognitive Services Fejlesztői magatartási kódexet](https://azure.microsoft.com/support/legal/developer-code-of-conduct/)”. 

A VideoFrameAnalyzer kép, hang, videó és szöveg felismerő funkciói a Microsoft Cognitive Services szolgáltatásokat használják. A Microsoft meg fogja kapni az (alkalmazáson keresztül) feltöltött képet, hangot, videót és egyéb adatokat és felhasználhatja őket szolgáltatás javítási célokra. A segítségét kérjük az emberek védelméhez, akiknek adatait az alkalmazása elküldi a Microsoft Cognitive Services szolgáltatásokhoz. 

## <a name="summary"></a>Összegzés

Ebben az útmutatóban megismerhette, hogyan lehet a Face, Computer Vision és Emotion API-k használatával közel valós idejű elemzéseket futtatni élő videó adatfolyamon, és látta, hogyan használhatja mintakódunkat a kezdéshez.  Alkalmazása készítését ingyenes API-kulccsal elkezdheti a [Microsoft Cognitive Services regisztrációs oldalán](https://azure.microsoft.com/try/cognitive-services/). 

Visszajelzéseket és javaslatokat örömmel fogadunk a [GitHub adattárban](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/), tágabb API visszajelzéseket pedig a [UserVoice oldalunkon](https://cognitive.uservoice.com/).

## <a name="related-topics"></a>Kapcsolódó témakörök
- [Hogyan lehet arcokat azonosítani a képen](HowtoIdentifyFacesinImage.md)
- [Hogyan lehet arcokat megtalálni a képen](HowtoDetectFacesinImage.md)
