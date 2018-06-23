---
title: A Érzelemfelismerési API meghívása videó |} Microsoft Docs
description: Útmutató a Érzelemfelismerési API meghívása videó kognitív szolgáltatásban.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 02/06/2017
ms.author: anroth
ms.openlocfilehash: 0875013b2061a84e3e23ae90c1106382672fdca6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347582"
---
# <a name="how-to-call-emotion-api-for-video"></a>Hogyan hívhatja meg Érzelemfelismerési API videó

> [!IMPORTANT]
> Villámnézet API a 2017. október 30 véget ér. Kipróbálhatja az új [videó indexelő API előnézete](https://azure.microsoft.com/services/cognitive-services/video-indexer/) insights könnyen kibontani videók, és tartalom felderítési lép, például a keresési eredmények, növelje a szóbeli szavakat, a lapok, a karakterek és a érzelmek észlelésével. [További információk](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Ez az útmutató bemutatja, hogyan videó Érzelemfelismerési API hívásához használandó. A minták Érzelemfelismerési API használatával videó ügyféloldali kódtára a C# nyelven íródtak.

### <a name="Prep">Előkészítése</a> 
Ahhoz, hogy a Érzelemfelismerési API videó, szüksége lesz egy videót, amely tartalmazza a személyek, lehetőleg videó ahol személyeket használata során szembesülnek a kamera.

### <a name="Step1">1. lépés: Engedélyezze az API-hívás</a> 
Videó az Érzelemfelismerési API-t minden hívásakor előfizetés kulcs szükséges. Ez a kulcs vagy a lekérdezési karakterlánc paraméterként keresztül vagy a kérelem fejlécében megadott kell. Az Előfizetés kulcs egy lekérdezési karakterlánc keresztül továbbítani, tekintse meg alatt a kérelem URL-CÍMÉT a Érzelemfelismerési API-videót például:

```
https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognizeInVideo&subscription-key=<Your subscription key>
```

Alternatív megoldásként az Előfizetés kulcs is megadható a kérelem HTTP-fejlécben:

```
ocp-apim-subscription-key: <Your subscription key>
```

Egy ügyféloldali kódtár használata esetén az Előfizetés kulcs áthalad a VideoServiceClient osztály konstruktor. Példa:

```
var emotionServiceClient = new emotionServiceClient("Your subscription key");
```
Egy előfizetés kulcs beszerzése, tekintse meg a [előfizetések] (https://azure.microsoft.com/try/cognitive-services/). 

### <a name="Step2">2. lépés: A szolgáltatás videó feltöltése, és tekintse meg</a>
A legalapvetőbb hajtsa végre a Érzelemfelismerési API videó hívások módja a videó közvetlenül feltöltésével. Ez történik, hogy az application/octet-stream tartalomtípus együtt a videofájl adatsorból beolvasott adatok "POST" kérelmet küld. A videó maximális mérete 100MB.

Az ügyféloldali kódtár használatával, stabilizációs feltöltése segítségével végezhető el az adatfolyam-objektummá sikeres. Lásd az alábbi példát:

```
Operation videoOperation;
using (var fs = new FileStream(@"C:\Videos\Sample.mp4", FileMode.Open))
{
      videoOperation = await videoServiceClient.CreateOperationAsync(fs, OperationType.recognizeInVideo);
}
```

Vegye figyelembe, hogy a CreateOperationAsync VideoServiceClient módszer aszinkron. A hívó metódus állapotúként kell megjelölni aszinkron, valamint ahhoz, hogy a await záradékban.
Ha a videó már a weben, és egy nyilvános URL-címe van, az URL-cím megadásával Érzelemfelismerési API-t a videó is elérhető. Ebben a példában a kérés törzsében lesz az URL-címet tartalmazó JSON-karakterlánc.

Az ügyféloldali kódtár használatával, stabilizációs egy URL-cím segítségével egyszerűen hajtható végre másik többszörös definíciót a CreateOperationAsync módszer használatával.


```
var videoUrl = "http://www.example.com/sample.mp4";
Operation videoOperation = await videoServiceClient.CreateOperationAsync(videoUrl, OperationType. recognizeInVideo);

```

A fájlfeltöltési módszer videó hívások Érzelemfelismerési API ugyanaz lesz. 

A feltöltést videó követően-érdemes hajtsa végre a következő művelet hoz állapota. Mivel videofájlok általában nagyobb és több különböző, mint más fájlok, a felhasználók számíthat hosszú ebben a lépésben feldolgozási ideje. Idő függ a méretét és a fájl hosszát.

Az ügyféloldali kódtár használatával, kérheti le a műveleti állapotának és az eredményt a GetOperationResultAsync metódussal.


```
var operationResult = await videoServiceClient.GetOperationResultAsync(videoOperation);

```
Általában ügyféloldali kell rendszeresen töltik le a műveletállapot állapota "Sikeres" vagy "Sikertelen" jelenik meg.

```
OperationResult operationResult;
while (true)
{
      operationResult = await videoServiceClient.GetOperationResultAsync(videoOperation);
      if (operationResult.Status == OperationStatus.Succeeded || operationResult.Status == OperationStatus.Failed)
      {
           break;
      }

      Task.Delay(30000).Wait();
}

```

Ha VideoOperationResult állapota megjelenik-e, a "Sikeres" az eredmény lekérhetők a egy VideoOperationInfoResult VideoOperationResult leadó<VideoAggregateRecognitionResult> és elérése a ProcessingResult mező.

```
var emotionRecognitionJsonString = ((VideoOperationInfoResult<VideoAggregateRecognitionResult>)operationResult).ProcessingResult;
```

### <a name="Step3">3. lépés: Beolvasása és a érzelemfelismerési felismerés ismertetése és JSON-kimenetét nyomon követése</a>

A kimeneti eredmények tartalmazza a megadott fájl JSON formátumú lapjaira a metaadatok.

2. lépésben leírtak JSON-kimenetét esetén érhető el az operationresult adatokat a, ProcessingResult mezőjében jelenik meg az állapota "Sikeres".

A tapasztalt észlelési és nyomon követni a JSON tartalmazza a következő attribútumokat:

Attribútum | Leírás
-------------|-------------
Verzió | Videó JSON hivatkozik a Érzelemfelismerési API verziója.
Időskálára | A videó másodpercenként "ticks".
Eltolás  |Eltolódás az időbélyegekhez. Érzelemfelismerési API-t a videók 1.0-s verziójában az mindig 0 lesz. A jövőbeli támogatott forgatókönyveket ezt az értéket módosíthatja.
Képkockasebességhez | A videó képkockasebessége.
Töredék   | A metaadatok töredék nevű másik kisebb darabokra darabolni be. Minden töredéke a start, a időtartama, a időszakának száma és a esemény (eke) tartalmaz.
Indítás   | Az első esemény a ticks kezdési idejét.
Időtartam |  A töredéke a ticks hosszát.
Időköz |  A töredéke a ticks belül mindegyik esemény hosszát.
Események  | Események tömbjét. A külső tömb egy időköz jelöli. A belső tömb 0 vagy több olyan eseményeket, amelyek ezen a ponton az időben történtek áll.
windowFaceDistribution |    Szeretné, hogy az esemény során egy adott érzelemfelismerési százalékos lapokat.
windowMeanScores |  Az egyes, a kép lapok érzelemfelismerési átlagos pontszámait.

A formázás a JSON így oka beállítása jövőbeli forgatókönyvek esetén az API-k legyenek fontos, hogy a metaadatok lekérése gyorsan és az eredmények nagy adatfolyam kezeléséhez. A formázás mindkét technikát töredezettsége (így szakítsa meg a metaadatok időalapú részre, ahonnan letöltheti csak találja), és Szegmentálás (hogy lehetővé teszi a feloszthatja az eseményeket, ha túl nagy) használja. Néhány egyszerű számítások segítségével átalakíthatja az adatokat. Például, ha egy esemény használatába 6300 (ticks), egy időskálára 2997 (ticks/másodperc), és a 29,97 (keret/mp), majd egy képkockasebességhez:

*   Start/időskálára = 2.1 másodperc
*   Másodperc (képkockasebességhez/időskálára) x 63 keretek =

Az alábbiakban az kibontása a JSON-kódot egy egyszerű példája egy keretformátum arcfelismerési észlelés és követési száma:

```
var emotionRecognitionTrackingResultJsonString = operationResult.ProcessingResult;
var emotionRecognitionTracking = JsonConvert.DeserializeObject<EmotionRecognitionResult>(emotionRecognitionTrackingResultJsonString, settings);
```
Érzelmek Ha valaha is átfedő felül az eredeti videó az eredményeket a képi megjelenítés Görbített adott idő alatt, a rendszer, mert a kivonás a megadott időbélyeg helyi időre való 250 ezredmásodperc.

### <a name="Summary">Összefoglalás</a>
A jelen útmutató megtanulta, a Érzelemfelismerési API funkciókkal kapcsolatos videót: hogyan feltöltheti a videót, ellenőrizze annak állapotát, érzelemfelismerési felismerés metaadatainak lekéréséhez.

API részletek kapcsolatos további információkért lásd: az API a referencia-útmutató "[Érzelemfelismerési API-t a videó hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/56f8d40e1984551ec0a0984e)".
