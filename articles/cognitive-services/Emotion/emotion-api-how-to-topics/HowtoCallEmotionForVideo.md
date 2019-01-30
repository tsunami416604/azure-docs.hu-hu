---
title: 'Példa: Az Emotion API meghívása videókhoz'
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hívható meg az Emotion API for Video a Cognitive Servicesben.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: sample
ms.date: 02/06/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: dd2df32ed43fd540a0516b7d5c1debc6a4f49f4f
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55211168"
---
# <a name="example-call-emotion-api-for-video"></a>Példa: Az Emotion API meghívása videókhoz

> [!IMPORTANT]
> Az Emotion API 2019. február 15-ével elavulttá válik. Az érzelemfelismerési képesség mostantól általánosan elérhető a [Face API](https://docs.microsoft.com/azure/cognitive-services/face/) részeként. 

Ez az útmutató bemutatja, hogy hívható meg az Emotion API for Video. A minták C# nyelven lettek megírva az Emotion API for Video ügyfélkódtárának használatával.

### <a name="Prep">Előkészítés</a>
Az Emotion API for Video használatához szüksége lesz egy videóra, amelyen lehetőleg a kamera felé néző személyek szerepelnek.

### <a name="Step1">1. lépés: Engedélyezze az API-hívás</a>
Az Emotion API for Video minden meghívásához szükség van egy előfizetői azonosító. Ezt a kulcsot egy lekérdezésisztring-paraméteren keresztül kell továbbítani, vagy a kérelemfejlécben kell megadni. Az előfizetői azonosító lekérdezési sztringen keresztül történő továbbításához tekintse meg az alábbi példát, amely egy kérés URL-címét tartalmazza az Emotion API for Videóhoz:

```
https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognizeInVideo&subscription-key=<Your subscription key>
```

Másik lehetőségként az előfizetői azonosítót a HTTP-kérés fejlécében is megadhatja:

```
ocp-apim-subscription-key: <Your subscription key>
```

Ügyfélkódtár használatakor az előfizetői azonosító átadása a VideoServiceClient osztály konstruktorán keresztül történik. Példa:

```
var emotionServiceClient = new emotionServiceClient("Your subscription key");
```
Előfizetési kulcs beszerzéséhez tekintse meg az [Előfizetések](https://azure.microsoft.com/try/cognitive-services/) című témakört.

### <a name="Step2">2. lépés: Videó feltöltése a szolgáltatáshoz, és az állapot ellenőrzése</a>
Az Emotion API for Video meghívásának legalapvetőbb módja a videó közvetlen feltöltése. Ezt egy alkalmazás-/oktettstream tartalomtípusú „POST” kérés és a videofájlból beolvasott adatok együttes elküldésével teheti meg. A videó maximális mérete 100 MB lehet.

Az ügyfélkódtár használata esetén a feltöltés általi stabilizáció egy streamobjektum átadásával történik. Lásd az alábbi példát:

```
Operation videoOperation;
using (var fs = new FileStream(@"C:\Videos\Sample.mp4", FileMode.Open))
{
      videoOperation = await videoServiceClient.CreateOperationAsync(fs, OperationType.recognizeInVideo);
}
```

Vegye figyelembe, hogy a VideoServiceClient CreateOperationAsync metódusa aszinkron. A várakozási záradék használatához a meghívási metódust is aszinkronként kell megjelölni.
Ha a videó már elérhető a weben, és rendelkezik nyilvános URL-címmel, az Emotion API for Video az URL-cím megadásával is elérhető. Ebben a példában a kéréstörzs egy JSON-sztring lesz, amely tartalmazza az URL-címet.

Az ügyfélkódtár használata esetén az URL-cím általi stabilizáció könnyedén végrehajtható a CreateOperationAsync metódus egy másik túlterhelésével.


```
var videoUrl = "http://www.example.com/sample.mp4";
Operation videoOperation = await videoServiceClient.CreateOperationAsync(videoUrl, OperationType. recognizeInVideo);

```

Ez a feltöltési metódus ugyanaz lesz az Emotion API for Video minden meghívásának esetében.

A videó feltöltése után következő műveletként érdemes ellenőrizni a videó állapotát. Mivel a videofájlok általában nagyobbak és változatosabbak más fájloknál, ennél a lépésnél a felhasználók hosszú feldolgozási időre számíthatnak. Az idő a fájl méretétől és hosszúságától függ.

Az ügyfélkódtár használata esetén a művelet állapotát és eredményét a GetOperationResultAsync metódussal kérheti le.


```
var operationResult = await videoServiceClient.GetOperationResultAsync(videoOperation);

```
Az ügyféloldal általában időről időre lekéri a művelet állapotát, amíg az „Succeeded” (Sikeres) vagy „Failed” (Sikertelen) értékű nem lesz.

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

Amikor a VideoOperationResult állapota „Succeeded” (Sikeres) lesz, az eredményt le lehet kérni a VideoOperationResult metódus VideoOperationInfoResult<VideoAggregateRecognitionResult> metódussá történő konvertálásával és a ProcessingResult mező elérésével.

```
var emotionRecognitionJsonString = ((VideoOperationInfoResult<VideoAggregateRecognitionResult>)operationResult).ProcessingResult;
```

### <a name="Step3">3. lépés: Beolvasása és a érzelemfelismerésre alkalmas környezetek ismertetése és nyomon követése a JSON-kimenet</a>

A kimeneti eredmény az adott fájlban található arcok metaadatait tartalmazza JSON formátumban.

Ahogy a 2. lépésben már említettük, a JSON-kimenet az OperationResult metódus ProcessingResult mezőjében érhető el, amikor az állapota „Succeeded” (Sikeres).

Az arcfelismerési és -követési JSON a következő attribútumokat tartalmazza:

Attribútum | Leírás
-------------|-------------
Verzió | Az Emotion API for Video JSON-verziójára hivatkozik.
Időskála | Másodpercenkénti „órajel” a videóban.
Eltolás  |Időeltolás az időbélyegek esetében. Az Emotion API for Video 1.0-s verziójában ennek az értéke mindig 0 lesz. Későbbi támogatott forgatókönyvekben ez az érték módosulhat.
Képkockasebesség | Képkockák másodpercenkénti száma a videóban.
Töredékek   | A metaadatok több kisebb részre, úgynevezett töredékre vannak feldarabolva. Minden töredék tartalmaz kezdési időpontot, időtartamot, intervallumszámot és esemény(eke)t.
Indítás   | Az első esemény kezdési időpontja órajelekben.
Időtartam |  A töredék hossza órajelekben.
Intervallum |  A töredékben található egyes események hossza órajelekben.
Események  | Események tömbje. A külső tömb egy időintervallumot jelöl. A belső tömb 0 vagy több eseményből áll, amelyek az adott időpontban történtek.
windowFaceDistribution |    Az esemény során egy bizonyos érzelmet kifejező arcok százaléka.
windowMeanScores |  A képen szereplő arcok egyes érzelmeinek átlagpontszáma.

A JSON azért van így formázva, hogy az API-k be legyenek állítva az olyan későbbi forgatókönyvekhez, amelyekben fontos lesz a metaadatok gyors lekérése és a nagy méretű eredménystreamek kezelése. Ez a formázás feldarabolási (ez teszi lehetővé, hogy a metaadatokat időalapú részekre bontsa fel, ahol csak azt tölti le, amire szüksége van), illetve szegmentálási (ez teszi lehetővé, hogy az eseményeket felbontsa, ha azok túl nagy méretűvé válnak) technikákat is alkalmaz. Néhány egyszerű számítás segítségére lehet az adatok átalakításában. Ha például egy esemény kezdési időpontja 6300 (órajel), időskálája 2997 (órajel/másodperc) és képkockasebessége 29,97 (képkocka/másodperc), akkor:

*   kezdési időpont/időskála = 2,1 másodperc
*   másodpercek x (képkockasebesség/időskála) = 63 képkocka

Az alábbiakban a JSON képkockánkénti formátumú kinyerésének egyszerű példáját láthatja arcfelismerés és -követés esetén:

```
var emotionRecognitionTrackingResultJsonString = operationResult.ProcessingResult;
var emotionRecognitionTracking = JsonConvert.DeserializeObject<EmotionRecognitionResult>(emotionRecognitionTrackingResultJsonString, settings);
```
Az érzelmek idővel kisimulnak, ezért ha egyszer létrehoz egy vizualizációt azzal a céllal, hogy eredményei átfedjék az eredeti videót, vonjon le 250 ezredmásodpercet a megadott időbélyegekből.

### <a name="Summary">összegzés</a>
Ebben az útmutatóban megismerte az Emotion API for Video funkcióit: a videófeltöltést, a videó állapotának ellenőrzését és az érzelemfelismerési metaadatok lekérését.

Az API részleteivel kapcsolatos további információért tekintse meg az „[Emotion API for Video-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/56f8d40e1984551ec0a0984e)” című API referencia-útmutatót.
