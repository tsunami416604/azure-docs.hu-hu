---
title: Optikai karakterfelismerés (OCR) – Computer Vision
titleSuffix: Azure Cognitive Services
description: A képek és dokumentumok optikai karakterfelismeréssel (OCR) kapcsolatos fogalmak a kinyomtatott és kézzel írott szöveggel a Computer Vision API használatával.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: cbcfddcd02a3998b3b35b01d386816735c59ae7e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90526408"
---
# <a name="optical-character-recognition-ocr"></a>Optikai karakterfelismerés (OCR)

Az Azure Computer Vision API olyan optikai karakterfelismerési (OCR) képességeket tartalmaz, amelyek kinyerik a képekből nyomtatott vagy kézírásos szöveget. Kinyerheti a képeket a képekből, például a licenccel rendelkező, a sorozatszámokkal, a dokumentumokból, a számlákból, a pénzügyi jelentésekről, a cikkekről és egyéb cikkekből származó képekből is.

## <a name="read-api"></a>API olvasása 

A Computer Vision [READ API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) az Azure legújabb OCR-technológiája (Ismerje meg az[újdonságokat](./whats-new.md)), amelyek kinyerik a nyomtatott szöveget (több nyelven), a kézírásos szövegeket (csak angol nyelven), számjegyeket és pénznem szimbólumokat a képekből és a többoldalas PDF-dokumentumokból. A szolgáltatás úgy van optimalizálva, hogy szövegből származó képekből és többoldalas PDF-dokumentumokból kinyerje a kevert nyelveket. Támogatja mind a nyomtatott, mind a kézírásos szöveg észlelését ugyanabban a képen vagy dokumentumban.

![Hogyan alakítja át az OCR a képeket és a dokumentumokat strukturált kimenetre a kinyert szöveggel](./Images/how-ocr-works.svg)

## <a name="input-requirements"></a>Bemeneti követelmények
Az **olvasási** hívás a képeket és a dokumentumokat veszi fel bemenetként. A követelmények a következők:

* Támogatott fájlformátumok: JPEG, PNG, BMP, PDF és TIFF
* PDF-és TIFF-fájlok esetén akár 2000-oldalas (csak az ingyenes szinthez tartozó első két oldal) lesz feldolgozva.
* A fájlméretnek kevesebbnek kell lennie, mint 50 MB (4 MB az ingyenes szinten), és legalább 50 x 50 képpont és legfeljebb 10000 x 10000 képpont méretűnek kell lennie. 
* A PDF-méreteknek legfeljebb 17 x 17 hüvelyknek kell lenniük, amely a jogi vagy az A3-as papírméretnek felel meg, és kisebb.

### <a name="read-31-preview-allows-selecting-pages"></a>Olvasási 3,1 előzetes verzió lehetővé teszi az oldal (ok) kiválasztását
Az [olvasási 3,1 előzetes API](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005)-val a nagyméretű többoldalas dokumentumok esetében adott oldalszámokat vagy oldalszámozást adhat meg bemeneti paraméterként, amely csak az oldalakból származó szöveg kinyerésére szolgál. Ez egy új bemeneti paraméter a választható nyelvi paraméter mellett.

> [!NOTE]
> **Nyelvi bevitel** 
>
> Az [olvasási hívás](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) opcionális paramétert tartalmaz a nyelvhez. Ez a dokumentum szövegének BCP-47 nyelvi kódja. Az olvasás támogatja az automatikus nyelvi azonosítást és a többnyelvű dokumentumokat, ezért csak akkor adjon meg egy nyelvi kódot, ha a dokumentumot adott nyelven szeretné feldolgozni.

## <a name="the-read-call"></a>Az olvasási hívás

Az olvasási API [olvasási hívása](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) egy képet vagy PDF-dokumentumot használ a bemenet és a szöveg aszinkron módon történő kibontásához. A hívás visszatérési értéke egy nevű válasz fejléc mező `Operation-Location` . Az `Operation-Location` érték egy URL-cím, amely a következő lépésben használandó műveleti azonosítót tartalmazza.

|Válasz fejléce| Eredmény URL-címe |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.0/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Számlázás** 
>
> A [Computer Vision díjszabási](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) oldala tartalmazza a beolvasott árképzési szintet. Minden elemzett rendszerkép vagy lap egy tranzakció. Ha a műveletet a 100 oldalakat tartalmazó PDF-vagy TIFF-dokumentummal hívja meg, akkor az olvasási művelet 100 tranzakcióként fog megjelenni, és az 100-tranzakciókért díjat számítunk fel. Ha 50 hívást kezdeményezett a műveletre, és mindegyik hívás egy 100-oldalas dokumentumot küldött be, akkor az 50 X 100 = 5000 tranzakcióért kell fizetnie.

## <a name="the-get-read-results-call"></a>Az olvasási eredmények beolvasása hívás

A második lépés az [olvasási eredmények lekérése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750) művelet hívása. Ez a művelet az olvasási művelet által létrehozott műveleti azonosító bemenetét veszi át. Egy olyan JSON-választ ad vissza, amely tartalmazza az **állapot** mezőt a következő lehetséges értékekkel. Ezt a műveletet a iteratív hívja meg, amíg vissza nem tér a **sikeres** értékkel. A másodpercenkénti kérések (RPS) mértékének meghaladása érdekében használjon 1 – 2 másodperces intervallumot.

|Mező| Típus | Lehetséges értékek |
|:-----|:----:|:----|
|status | sztring | notStarted: a művelet nem indult el. |
| |  | fut: a művelet feldolgozása folyamatban van. |
| |  | sikertelen: a művelet sikertelen volt. |
| |  | sikeres: a művelet sikeresen befejeződött. |

> [!NOTE]
> Az ingyenes szintek percenként 20 hívásra korlátozzák a kérelmek sebességét. A fizetős csomag másodpercenként 10 másodpercenkénti kérést (RPS) tesz lehetővé. Az Azure támogatási csatornájának vagy a fiókja csapatának használatával nagyobb kérést igényelhet másodpercenként (RPS).

Ha az **állapot** mező értéke **sikeres** , a JSON-válasz a rendszerképből vagy dokumentumból kinyert szöveges tartalmat tartalmazza. A JSON-válasz a felismert szavak eredeti sorát tárolja. Magában foglalja a kinyert szöveg sorait és a hozzájuk tartozó mezők koordinátáit. Minden szöveges sor tartalmazza az összes kinyert szót a koordinátáikkal és a megbízhatósági pontszámokkal.

## <a name="sample-json-output"></a>Példa JSON-kimenetre

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
### <a name="read-31-preview-adds-text-line-style-latin-languages-only"></a>A 3,1-es olvasási előnézet szöveg sortörést tesz elérhetővé (csak latin nyelveket)
Az [olvasási 3,1 előzetes verzió API](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005) kimenete egy **megjelenési** objektum, amely azt sorolja fel, hogy az egyes szövegfájlok nyomtatási vagy kézírási stílussal, valamint megbízhatósági pontszámmal rendelkeznek-e. Ez a funkció csak latin nyelveken támogatott.

```json
  "appearance": {
              "style": "handwriting",
              "styleConfidence": 0.836
            }
```
Ismerkedjen meg a [Computer Vision OCR SDK](./quickstarts-sdk/client-library.md) -gyors útmutatókkal és az [olvasási REST API](./QuickStarts/CSharp-hand-text.md) gyors üzembe helyezésével az OCR képességek integrálásához az alkalmazásokba.

## <a name="supported-languages-for-print-text"></a>A nyomtatási szöveghez támogatott nyelvek
Az [olvasási 3,0 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) támogatja a nyomtatott szövegek angol, spanyol, német, francia, olasz, portugál és holland nyelvű kinyerését.

Az OCR által támogatott nyelvek teljes listájáért tekintse meg a [támogatott nyelveket](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) .

### <a name="read-31-preview-adds-simplified-chinese-and-japanese"></a>Az 3,1-es olvasási előnézet egyszerűsített kínai és japán nyelvű
A [Read 3,1 API nyilvános előzetes verziója](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005) támogatja az egyszerűsített kínai és Japán nyelveket. Ha a forgatókönyv további nyelvek támogatását igényli, tekintse meg az [OCR API](#ocr-api) című szakaszt. 

## <a name="supported-languages-for-handwritten-text"></a>A kézzel írt szöveghez támogatott nyelvek
Az olvasási művelet jelenleg kizárólag angol nyelven támogatja a kézzel írt szöveg kinyerését.

## <a name="use-the-rest-api-and-sdk"></a>A REST API és az SDK használata
Az [olvasás 3. x REST API](./QuickStarts/CSharp-hand-text.md) az előnyben részesített lehetőség a legtöbb ügyfél számára, mivel a könnyű integráció és a gyors termelékenység a box-ban. Az Azure és a Computer Vision szolgáltatás kezeli a méretezést, a teljesítményt, az adatbiztonságot és a megfelelőségi igényeket, miközben az ügyfelek igényeinek kielégítésére koncentrál.

## <a name="deploy-on-premise-with-docker-containers"></a>Helyszíni üzembe helyezés Docker-tárolókkal
Az [olvasási 2,0 Docker-tároló (előzetes verzió)](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers) lehetővé teszi az új OCR-képességek üzembe helyezését a saját helyi környezetében. A tárolók kiválóan alkalmasak adott biztonsági és adatszabályozási követelményekhez.

## <a name="example-outputs"></a>Példa kimenetekre

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

> [!NOTE]
> A vison 2,0 RecognizeText művelet folyamatban van a jelen cikkben ismertetett új olvasási API Javához. A meglévő ügyfeleknek [át kell térniük az olvasási műveletek használatára](upgrade-api-versions.md).

## <a name="next-steps"></a>Következő lépések

- Ismerkedjen meg a [Computer Vision Read 3,0 SDK](./quickstarts-sdk/client-library.md) -gyors útmutatóval C#, Java, JavaScript vagy Python nyelven.
- A REST API-k használatával megtudhatja, hogyan használhatja a [Read 3,0 REST API](./QuickStarts/CSharp-hand-text.md) a C#, a Java, a JavaScript vagy a Python segítségével.
- További információ az [olvasási 3,0 Rest APIról](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005).
- Ismerkedjen meg az [olvasási 3,1 nyilvános előzetes](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005) verziójával REST API az egyszerűsített kínai és Japán támogatással kiegészítve.
