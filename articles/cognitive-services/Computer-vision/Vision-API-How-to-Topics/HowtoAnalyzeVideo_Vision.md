---
title: 'Példa: Valós idejű videóelemzés a Computer Vision API-val'
titlesuffix: Azure Cognitive Services
description: Megtanulhatja, hogyan végezhet közel valós idejű elemzést egy élő videó-adatfolyam képkockáin a Computer Vision API segítségével.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: sample
ms.date: 01/20/2017
ms.author: kefre
ms.openlocfilehash: 058f2ad58665a88d2d3cf3ce20b43ac0fad30000
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "45983195"
---
# <a name="how-to-analyze-videos-in-real-time"></a>Videók valós idejű elemzése
Ez az útmutató azt ismerteti, hogyan végezhet közel valós idejű elemzést egy élő videó-adatfolyam képkockáin. Egy ilyen rendszer alapvető összetevői a következők:

- A képkockák kinyerése a videóforrásból
- Az elemezni kívánt képkockák kiválasztása
- A képkockák elküldése az API-nak
- Az API-hívás összes visszaadott elemzési eredményének feldolgozása

Ezek a minták a C# nyelven íródtak, és a kódjuk a következő GitHub-címen érhető el: [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

## <a name="the-approach"></a>A módszer
Többféle módon is meg lehet közelíteni a videó-adatfolyamok közel valós idejű elemzésének problémáját. Három különböző megközelítést mutatunk be az egyszerűbbtől az összetettebbekig.

### <a name="a-simple-approach"></a>Az egyszerű megközelítés
A valós idejű elemzési rendszer legegyszerűbb megvalósítása egy végtelen ciklus használata, amelyben minden iterációnál kiragadunk egy képkockát, elemezzük, majd feldolgozzuk az eredményt:
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
Egy egyszerű egyszálas ciklus megfelelő lehet, ha egy kis méretű ügyféloldali algoritmusról van szó, de felhőbeli API-hívások esetén a késés miatt már nem igazán használható. Ezt a problémát úgy oldhatjuk meg, ha lehetővé tesszük, hogy a hosszú futási idejű API-hívások párhuzamosan fussanak a képkockarögzítéssel. A C#-ban ezt a feladatalapú párhuzamos végrehajtás használatával érhetjük el, például így:
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
Ez a megoldás az elemzéseket egy-egy különálló feladatban indítja el, amelyek tovább futnak a háttérben, amíg mi újabb képkockákat rögzítünk. Ezzel a megoldással elkerüljük a fő szál blokkolását, amíg az API-hívások válaszára várunk, viszont elvesztünk néhány garanciát, amelyeket az egyszerű megoldás biztosított számunkra – vagyis mivel több API-hívás is futhat egyszerre párhuzamosan, az eredmények lehet, hogy rossz sorrendben érkeznek vissza. Ennél a megközelítésnél az is megtörténhet, hogy több szál lép be a ConsumeResult() függvénybe egyidejűleg, ami veszélyes lehet, ha a függvény nem szálbiztos. Végezetül ez a kód nem követi nyomon a létrehozott feladatokat sem, így a kivételek csendben eltűnnek. Ezért az utolsó összetevő, amit hozzá kell adnunk a megoldáshoz, egy "fogyasztó" szál, ami nyomon követi az elemzést, elkapja a kivételeket, leállítja a hosszú ideig futó feladatokat, és biztosítja, hogy az eredmények feldolgozása jó sorrendben egyenként történjen.

### <a name="a-producer-consumer-design"></a>Az előállító-fogyasztó alapú megközelítés
A végső "előállító-fogyasztó" alapú rendszerünkben van egy előállító szál, amely a korábbi végtelen ciklusunkhoz hasonlít. Azonban ahelyett, hogy az érkező elemzési eredményeket azonnal feldogozná, az előállító csupán egy sorba rakja a feladatokat, hogy azokat nyomon követhesse.
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
Van egy fogyasztói szálunk is, ami a sorból kiveszi a feladatokat, megvárja, amíg befejeződnek, majd vagy megjeleníti az eredményeket, vagy előlépteti a dobott kivételt. A várakozási sor használatával garantálni tudjuk, hogy az eredmények feldolgozása egyesével, jó sorrendben történjen és anélkül, hogy a rendszer maximális képkocka-sebességét korlátoznánk.
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
Azért, hogy alkalmazása a lehető leghamarabb elkészüljön és futtatni lehessen, úgy valósítottuk meg a fent leírt rendszert, hogy az elég rugalmas legyen többféle forgatókönyv megvalósításához is, de közben könnyen használható is legyen. A kód itt érhető el: [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis).

A kódtár tartalmaz egy FrameGrabber osztályt, ami a webkameráról érkező videóképkockák feldolgozásához a fent tárgyalt előállító-fogyasztó rendszert valósítja meg. A felhasználó megadhatja az API-hívás pontos formáját, az osztály események használatával tudatja a hívó kóddal, amikor új képkockára tett szert vagy amikor egy új elemzési eredmény válik elérhetővé.

A lehetőségek illusztrálására két, ezen a kódtáron alapuló mintaalkalmazás mutatunk be. Az első egy egyszerű konzolalkalmazás, amelynek egyszerűsített változata lentebb látható. Rögzíti az alapértelmezett webkamera képkockáit, és elküldi őket arcfelismerésre a Face API-nak.
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
A második mintaalkalmazás kicsit érdekesebb, ennél választhat, hogy melyik API-t hívja meg a videó képkockáira. A bal oldalon az alkalmazás az élő videó előnézetét jeleníti meg, a jobb oldalon pedig a hozzátartozó képkockán a legfrissebb API-eredmény látható.

A legtöbb módban látható késés tapasztalható a bal oldali élő videó és a jobb oldali vizualizált elemzési eredmény között. Ez a késés az API-hívás végrehajtásához szükséges idő. Kivétel ez alól a "EmotionsWithClientFaceDetect" mód, amely azelőtt, hogy képeket küldene a Cognitive Services számára, előbb az ügyfélszámítógépen helyileg arcfelismerést végez az OpenCV használatával. Ily módon a felismert arcot azonnal megjeleníthetjük, az érzelmet pedig később frissíthetjük, amikor az API-hívás visszatér a válasszal. Ez jól szemlélteti egy "hibrid" megközelítés lehetőségét, ahol néhány egyszerű adatfeldolgozást az ügyfélszámítógép végez, az eredmény pedig a Cognitive Services API-k fejlettebb elemzéseinek használatával szükség szerint tovább finomítható.

![HowToAnalyzeVideo](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>Beépítés a kódbázisba
A minta használatának megkezdéséhez kövesse a következő lépéseket:

1. Szerezze be a Vision API-khoz használható API-kulcsait az [Előfizetések](https://azure.microsoft.com/try/cognitive-services/) területen. Videóképkocka-elemzéshez használható API-k a következők:
    - [Computer Vision API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [Emotion API](https://docs.microsoft.com/azure/cognitive-services/emotion/home)
    - [Face API](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. Klónozza a [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) GitHub-adattárat

3. Nyissa meg a mintát a Visual Studio 2015-ben, készítsen buildet, majd futtassa a mintaalkalmazásokat:
    - A BasicConsoleSample esetén a Face API-kulcs nem változtatható, csak közvetlenül a [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs) programfájlban.
    - A LiveCameraSample esetén a kulcsokat az alkalmazás beállítások panelén lehet megadni. Ezek felhasználói adatként a munkamenetek között is megőrződnek.
        

Ha készen áll a integrációra, **egyszerűen hivatkozzon saját projektjéből a VideoFrameAnalyzer könyvtárra.** 



## <a name="developer-code-of-conduct"></a>Fejlesztői magatartási kódex
Mint valamennyi Cognitive Services-szolgáltatás esetében, az API-jainkat használó fejlesztőknek követniük kell a "[Microsoft Cognitive Services Fejlesztői magatartási kódex](https://azure.microsoft.com/support/legal/developer-code-of-conduct/)" iránymutatásait. 


A VideoFrameAnalyzer kép-, hang-, videó- és szövegértelmező funkciói a Azure Cognitive Services-szolgáltatásokat használják. A Microsoft az alkalmazáson keresztül feltöltött képeket, hangokat, videókat és egyéb adatokat megkapja, és felhasználhatja őket a szolgáltatás javítása érdekében. Kérjük segítsen minket megvédeni azokat az embereket, akiknek az adatait az alkalmazása elküldi a Azure Cognitive Services számára. 


## <a name="summary"></a>Összegzés
Ebben az útmutatóban megismerhette, hogyan lehet a Face, Computer Vision és Emotion API-k használatával közel valós idejű elemzéseket futtatni élő videó-adatfolyamon, és láthatta, hogyan használhatja mintakódunkat a kezdéshez. Most már hozzákezdhet saját alkalmazás fejlesztéséhez is, amelyhez ingyenes API-kulcsokat a [Microsoft Cognitive Services regisztrációs oldalán](https://azure.microsoft.com/try/cognitive-services/) talál. 

Visszajelzéseket és javaslatokat örömmel fogadunk a [GitHub adattárban](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/), bővebb API-val kapcsolatos visszajelzéseket pedig a [UserVoice oldalunkon](https://cognitive.uservoice.com/) hagyhat.

