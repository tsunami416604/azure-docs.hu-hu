---
title: Optikai karakterfelismerés (OCR) – Computer Vision
titleSuffix: Azure Cognitive Services
description: Az optikai karakterfelismeréssel (OCR) kapcsolatos fogalmak a képekből és dokumentumokból nyomtatott és kézzel írott szöveggel a Computer Vision API használatával.
services: cognitive-services
author: msbbonsu
manager: netahw
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: t-bebon
ms.custom: seodec18
ms.openlocfilehash: 83e76cd96e09b0e136d2bfbe2e5863b289724bdd
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87284251"
---
# <a name="optical-character-recognition-ocr"></a>Optikai karakterfelismerés (OCR)

A Microsoft Computer Vision API olyan optikai karakterfelismerési (OCR) képességeket tartalmaz, amelyek nyomtatott vagy kézírásos szövegeket nyernek ki képekből és PDF-dokumentumokból. Az OCR API-k az analóg dokumentumok (képek, beolvasott dokumentumok) és a digitalizált dokumentumok szövegét is kinyerik. Szöveget is kinyerheti a képekből a vadonban, például a lemezekről vagy a sorozatszámokkal rendelkező tárolók, valamint a dokumentumok – számlák, számlák, pénzügyi jelentések, cikkek és egyebek között. Az új olvasási OCR API a felügyelt szolgáltatás részeként érhető el a felhőben vagy a helyszínen (tárolókban). Emellett támogatja a virtuális hálózatokat és a magánhálózati végpontokat a vállalati szintű megfelelőségi és adatvédelmi igények kielégítése érdekében.

## <a name="read-api"></a>API olvasása 

A Computer Vision [READ API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) a Microsoft legújabb OCR-technológiája, amely a nyomtatott szövegeket több nyelven, kézírásos szövegben (csak angol nyelven), számjegyeket és pénznem szimbólumokat gyűjti a képekből és a többoldalas PDF-dokumentumokból. Ez a funkció úgy van optimalizálva, hogy szövegből kinyert szöveg-és többoldalas PDF-dokumentumokból, vegyes nyelvekkel. Támogatja a nyomtatott és a kézírásos szöveg (csak angol nyelven) észlelését ugyanabban a képen vagy dokumentumban. Tekintse meg az [OCR által támogatott nyelvek](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) teljes listáját tartalmazó oldalt.

### <a name="how-ocr-works"></a>Az OCR működése

Az [OLVASÁSI API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) legfeljebb 2000 oldalas szöveget támogat, és így aszinkron módon hajtható végre. Az első lépés az olvasási művelet meghívása. Az olvasási művelet egy képet vagy PDF-dokumentumot vesz fel bemenetként, és visszaadja a művelet AZONOSÍTÓját. 

A második lépés az [eredmények lekérése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750) művelet meghívása. Ez a művelet a művelet AZONOSÍTÓját veszi át, amelyet az olvasási művelet hozott létre. Ezután visszaadja a kinyert szöveges tartalmat a rendszerképből vagy a dokumentumból JSON formájában. A JSON-válasz a felismert szavak eredeti sorát tárolja. Magában foglalja a kinyert szöveg sorait és a hozzájuk tartozó mezők koordinátáit. Minden szöveges sor tartalmazza az összes kinyert szót a koordinátáikkal és a megbízhatósági pontszámokkal.

Ha szükséges, olvassa el a felismert lap rotációs eltolásának kiírása a következő ábrán látható módon, a vízszintes képtengelyre vonatkozó fokban megadva.

![Hogyan alakítja át az OCR a képeket és a dokumentumokat strukturált kimenetre a kinyert szöveggel](./Images/how-ocr-works.svg)

### <a name="sample-ocr-output"></a>Minta OCR-kimenet

A sikeres választ a JSON-formátumban adja vissza a következő példában látható módon:

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

### <a name="input-requirements"></a>Bemeneti követelmények

Az olvasási API a következő bemeneteket veszi át:
* Támogatott fájlformátumok: JPEG, PNG, BMP, PDF és TIFF
* A PDF és a TIFF esetében akár 2000 oldal is feldolgozható. Ingyenes szintű előfizetők esetében csak az első két oldal lesz feldolgozva.
* A fájl méretének 50 MB-nál kisebbnek kell lennie, és legalább 50 x 50 képpont és legfeljebb 10000 x 10000 képpont méretűnek kell lennie.
* A PDF-méreteknek legfeljebb 17 x 17 hüvelyknek kell lenniük, amely a jogi vagy az A3-as papírméretnek felel meg, és kisebb.

### <a name="text-from-images"></a>Képekből származó szöveg

A következő olvasási API-kimenet megjeleníti a kinyert szöveges vonalakat és szavakat egy olyan képből, amely különböző nézőpontokból, színekről és betűtípusokról tartalmaz szöveget.

![Egy elforgatott kép és annak szövege olvasható és körülhatárolt](./Images/text-from-images-example.png)

### <a name="text-from-documents"></a>Dokumentumokból származó szöveg

A képeken kívül az olvasási API bemenetként fogad egy PDF-dokumentumot.

![Egy elforgatott kép és annak szövege olvasható és körülhatárolt](./Images/text-from-documents-example.png)


### <a name="handwritten-text-in-english"></a>Kézírásos szöveg angol nyelven

Jelenleg az olvasási művelet támogatja a kézzel írt szöveg kinyerését kizárólag angol nyelven.

![Egy elforgatott kép és annak szövege olvasható és körülhatárolt](./Images/handwritten-example.png)

### <a name="printed-text-in-supported-languages"></a>Kinyomtatott szöveg a támogatott nyelveken

Az olvasási 3,0 API támogatja a nyomtatott szövegek angol, spanyol, német, francia, olasz, portugál és holland nyelvű kinyerését. [Olvasás 3,1 – előzetes verzió. 1 az API nyilvános előzetes verziója](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005) támogatja az egyszerűsített kínai verziót. Ha a forgatókönyv további nyelvek támogatását igényli, tekintse meg a jelen dokumentum OCR API áttekintése című témakörét. Tekintse meg az [OCR által támogatott nyelvek](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) listáját

![Egy elforgatott kép és annak szövege olvasható és körülhatárolt](./Images/supported-languages-example.png)

### <a name="mixed-languages-support"></a>Vegyes nyelvek támogatása

Az olvasási API támogatja a több nyelvet tartalmazó képeket és dokumentumokat, amelyek gyakran más néven vegyes nyelvi dokumentumok. Ezt úgy végezheti el, hogy a szöveg tartalmának kibontása előtt osztályozza a dokumentumban lévő egyes szöveges vonalakat az észlelt nyelvre.

![Egy elforgatott kép és annak szövege olvasható és körülhatárolt](./Images/mixed-language-example.png)

### <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

Akárcsak az összes kognitív szolgáltatás esetében, az olvasási szolgáltatást használó fejlesztőknek ismerniük kell a Microsoft-szabályzatokat az ügyféladatok alapján. További információért tekintse meg a [Microsoft adatvédelmi Központjának](https://www.microsoft.com/en-us/trust-center/product-overview) Cognitive Services lapját.

### <a name="containers-for-on-premise-deployment"></a>Helyszíni üzembe helyezéshez használható tárolók

Az olvasás a Docker-tárolóként (előzetes verzió) is elérhető, amely lehetővé teszi az új OCR-képességek üzembe helyezését a saját környezetében. A tárolók az adott biztonsági és adatirányítási követelményekhez is kiválóak. Lásd: [olvasási tárolók telepítése és futtatása.](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)


## <a name="ocr-api"></a>OCR API

Az [OCR API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) egy régebbi felismerési modellt használ, csak a képeket támogatja, és szinkron módon hajtja végre az észlelt szöveggel való azonnali visszatérést. Tekintse meg az [OCR által támogatott nyelveket](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) , mint az olvasási API.

## <a name="next-steps"></a>További lépések

- További információ az [olvasási 3,0 Rest APIról](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005).
- Ismerkedjen meg az [Read 3,1 – preview. 1 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005) az egyszerűsített kínai támogatásával.
- A [szöveg kinyerése](./QuickStarts/CSharp-hand-text.md) rövid útmutatóval a C#, a Java, a JavaScript vagy a Python használatával, valamint a REST API segítségével implementálhatja az OCR-t.
