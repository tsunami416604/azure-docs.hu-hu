---
title: Optikai karakterfelismerés (OCR) – Computer Vision
titleSuffix: Azure Cognitive Services
description: A képek és dokumentumok optikai karakterfelismeréssel (OCR) kapcsolatos fogalmak a kinyomtatott és kézzel írott szöveggel a Computer Vision API használatával.
services: cognitive-services
author: PatrickFarley
manager: netahw
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: e2226f70ed3318bb370f0afee003fd9f91153a45
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88167868"
---
# <a name="optical-character-recognition-ocr"></a>Optikai karakterfelismerés (OCR)

Az Azure Computer Vision API olyan optikai karakterfelismerési (OCR) képességeket tartalmaz, amelyek kinyerik a képekből nyomtatott vagy kézírásos szöveget. Kinyerheti a képeket a képekből, például a licenccel rendelkező, a sorozatszámokkal, a dokumentumokból, a számlákból, a pénzügyi jelentésekről, a cikkekről és egyéb cikkekből származó képekből is. 

## <a name="read-api"></a>API olvasása 

A Computer Vision [READ API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) az Azure legújabb OCR-technológiája, amely Kinyeri a nyomtatott szöveget (több nyelven), a kézírásos szövegeket (csak angol nyelven), számjegyeket és pénznem szimbólumokat a képekből és a többoldalas PDF-dokumentumokból. A szolgáltatás úgy van optimalizálva, hogy szövegből származó képekből és többoldalas PDF-dokumentumokból kinyerje a kevert nyelveket. Támogatja mind a nyomtatott, mind a kézírásos szöveg észlelését ugyanabban a képen vagy dokumentumban.

![Hogyan alakítja át az OCR a képeket és a dokumentumokat strukturált kimenetre a kinyert szöveggel](./Images/how-ocr-works.svg)

Az olvasási API két művelet – **olvasás** és **olvasási eredmények**– használatával biztosít OCR-képességeket.

## <a name="the-read-operation"></a>Az olvasási művelet

Az [olvasási művelet](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) egy képet vagy PDF-dokumentumot használ a bemenetként, és aszinkron módon Kinyeri a szöveget. A hívás visszatérési értéke egy nevű válasz fejléc mező `Operation-Location` . Az `Operation-Location` érték egy URL-cím, amely a következő lépésben használandó műveleti azonosítót tartalmazza.

|Válasz fejléce| Eredmény URL-címe |
|:-----|:----|
|Művelet – hely | https://cognitiveservice/vision/v3.0-preview/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f |

## <a name="the-get-read-results-operation"></a>Az olvasási eredmények lekérése művelet

A második lépés az [olvasási eredmények lekérése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750) művelet meghívása. Ez a művelet az olvasási művelet által létrehozott műveleti azonosító bemenetét veszi át. Egy olyan JSON-választ ad vissza, amely tartalmazza az **állapot** mezőt a következő lehetséges értékekkel. Ezt a műveletet a iteratív hívja meg, amíg vissza nem tér a **sikeres** értékkel. A másodpercenkénti kérések (RPS) mértékének meghaladása érdekében használjon 1 – 2 másodperces intervallumot.

|Mező| Típus | Lehetséges értékek |
|:-----|:----:|:----|
|status | sztring | notStarted: a művelet nem indult el. |
| |  | fut: a művelet feldolgozása folyamatban van. |
| |  | sikertelen: a művelet sikertelen volt. |
| |  | sikeres: a művelet sikeresen befejeződött. |

> [!NOTE]
> Az ingyenes szintek percenként 20 hívásra korlátozzák a kérelmek sebességét. A fizetős csomag másodpercenként 10 másodpercenkénti kérést (RPS) tesz lehetővé. Az Azure támogatási csatornájának vagy a fiókja csapatának használatával nagyobb kérést igényelhet másodpercenként (RPS).

Ha az **állapot** mező értéke **sikeres** , a JSON-válasz a rendszerképből vagy dokumentumból kinyert szöveges tartalmat tartalmazza. A JSON-válasz a felismert szavak eredeti sorát tárolja. Magában foglalja a kinyert szöveg sorait és a hozzájuk tartozó mezők koordinátáit. Minden szöveges sor tartalmazza az összes kinyert szót a koordinátáikkal és a megbízhatósági pontszámokkal.

### <a name="sample-json-output"></a>Példa JSON-kimenetre

Tekintse meg a sikeres JSON-válasz következő példáját:

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-05-28T05:13:21Z",
  "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
  "analyzeResult": {
    "version": "3.0.0",
    "readResults": [
      {
        "page": 1,
        "language": "en",
        "angle": 0.8551,
        "width": 2661,
        "height": 1901,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              67,
              646,
              2582,
              713,
              2580,
              876,
              67,
              821
            ],
            "text": "The quick brown fox jumps",
            "words": [
              {
                "boundingBox": [
                  143,
                  650,
                  435,
                  661,
                  436,
                  823,
                  144,
                  824
                ],
                "text": "The",
                "confidence": 0.958
              }
            ]
          }
        ]
      }
    ]
  }
}
```

Kövesse a [nyomtatott és a kézzel írt szöveg kinyerése](./QuickStarts/CSharp-hand-text.md) az OCR a C# és a REST API használatával történő megvalósításához című témakört.

## <a name="input-requirements"></a>Bemeneti követelmények

A bemeneti képek és dokumentumok a következő követelményekkel rendelkeznek:
* Támogatott fájlformátumok: JPEG, PNG, BMP, PDF és TIFF
* A PDF és a TIFF esetében akár 2000 oldal is feldolgozható. Ingyenes szintű előfizetők esetében csak az első két oldal lesz feldolgozva.
* A fájl méretének 50 MB-nál kisebbnek kell lennie, és legalább 50 x 50 képpont és legfeljebb 10000 x 10000 képpont méretűnek kell lennie.
* A PDF-méreteknek legfeljebb 17 x 17 hüvelyknek kell lenniük, amely a jogi vagy az A3-as papírméretnek felel meg, és kisebb.

> [!NOTE]
> **Nyelvi bevitel** 
>
> Az [olvasási művelethez](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) választható lekérdezési paraméter tartozik a nyelvhez. Ez a dokumentum szövegének BCP-47 nyelvi kódja. Az olvasás támogatja az automatikus nyelvi azonosítást és a többnyelvű dokumentumokat, ezért csak akkor adjon meg egy nyelvi kódot, ha a dokumentumot adott nyelven szeretné feldolgozni.

## <a name="language-support"></a>Nyelvi támogatás

### <a name="printed-text"></a>Nyomtatott szöveg
Az [olvasási 3,0 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) támogatja a nyomtatott szövegek angol, spanyol, német, francia, olasz, portugál és holland nyelvű kinyerését. 

A [Read 3,1 API nyilvános előzetes verziója](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005) támogatja az egyszerűsített kínai verziót. Ha a forgatókönyv további nyelvek támogatását igényli, tekintse meg az [OCR API](#ocr-api) című szakaszt. 

Az OCR által támogatott nyelvek teljes listájáért tekintse meg a [támogatott nyelveket](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) .

### <a name="handwritten-text"></a>Kézírásos szöveg
Az olvasási művelet jelenleg kizárólag angol nyelven támogatja a kézzel írt szöveg kinyerését.

## <a name="integration-options"></a>Integrációs lehetőségek

### <a name="use-the-rest-api-or-client-sdk"></a>Az REST API vagy az ügyféloldali SDK használata
Az [olvasás 3. x REST API](./QuickStarts/CSharp-hand-text.md) az előnyben részesített lehetőség a legtöbb ügyfél számára, mivel a könnyű integráció és a gyors termelékenység a box-ban. Az Azure és a Computer Vision szolgáltatás kezeli a méretezést, a teljesítményt, az adatbiztonságot és a megfelelőségi igényeket, miközben az ügyfelek igényeinek kielégítésére koncentrál.

### <a name="use-containers-for-on-premise-deployment"></a>Tárolók használata helyi telepítéshez
Az [olvasási 2,0 Docker-tároló (előzetes verzió)](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers) lehetővé teszi az új OCR-képességek üzembe helyezését a saját helyi környezetében. A tárolók az adott biztonsági és adatirányítási követelményekhez is kiválóak.

## <a name="read-ocr-examples"></a>OCR-példák olvasása

### <a name="text-from-images"></a>Képekből származó szöveg

A következő olvasási API-kimenet egy olyan képből származó kinyert szöveget jelenít meg, amely különböző szöveges nézőpontokkal, színekkel és betűtípusokkal rendelkezik.

![Több, különböző színnel és nézőpontból álló szó képe, a kinyert szöveggel](./Images/text-from-images-example.png)

### <a name="text-from-documents"></a>Dokumentumokból származó szöveg

Az olvasási API bemenetként is készíthet PDF-dokumentumokat.

![Egy számlázási dokumentum, amely a kinyert szöveggel szerepel](./Images/text-from-pdf-example.png)

### <a name="handwritten-text"></a>Kézírásos szöveg

Az olvasási művelet beolvassa a kézzel írt szöveget a képekből (jelenleg csak angol nyelven).

![Egy kézzel írott jegyzet képe, a kinyert szöveggel](./Images/handwritten-example.png)

### <a name="printed-text"></a>Nyomtatott szöveg

Az olvasási művelet több különböző nyelven is kinyerheti a nyomtatott szöveget.

![Egy spanyol tankönyv képe, amelyben a kinyert szöveg szerepel](./Images/supported-languages-example.png)

### <a name="mixed-language-documents"></a>Vegyes nyelvi dokumentumok

Az olvasási API olyan képeket és dokumentumokat támogat, amelyek több különböző nyelvet tartalmaznak, gyakran más néven kevert nyelvi dokumentumokat. Úgy működik, hogy a dokumentumban lévő egyes szöveges vonalakat a szöveg tartalmának kibontása előtt osztályozza az észlelt nyelvre.

![Több nyelven írt mondatok képe, a kinyert szöveggel](./Images/mixed-language-example.png)

## <a name="ocr-api"></a>OCR API

Az [OCR API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) egy régebbi felismerési modellt használ, csak a képeket támogatja, és szinkron módon hajtja végre az észlelt szöveggel való azonnali visszatérést. Lásd az [OCR által támogatott nyelvek](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) , majd az API olvasása című témakört.

## <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

Akárcsak az összes kognitív szolgáltatás esetében, az olvasási/OCR szolgáltatást használó fejlesztőknek ismerniük kell a Microsoft-szabályzatokat az ügyféladatok alapján. További információért tekintse meg a [Microsoft adatvédelmi Központjának](https://www.microsoft.com/trust-center/product-overview) Cognitive Services lapját.

## <a name="next-steps"></a>További lépések

- További információ az [olvasási 3,0 Rest APIról](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005).
- Ismerkedjen meg az [olvasási 3,1 nyilvános előzetes](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005) verziójával REST API az egyszerűsített kínai támogatásával.
- A [szöveg kinyerése](./QuickStarts/CSharp-hand-text.md) rövid útmutatóval a C#, a Java, a JavaScript vagy a Python használatával, valamint a REST API segítségével implementálhatja az OCR-t.
