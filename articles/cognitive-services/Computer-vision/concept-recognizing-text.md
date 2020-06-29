---
title: Szöveg olvasása képekből és dokumentumokból – Computer Vision
titleSuffix: Azure Cognitive Services
description: Az optikai karakterfelismeréssel (OCR) és szöveggel kapcsolatos fogalmak a képekből és dokumentumokból a Computer Vision API használatával nyomtatott és kézírásos szövegekhez.
services: cognitive-services
author: msbbonsu
manager: netahw
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: t-bebon
ms.custom: seodec18
ms.openlocfilehash: 65e1613eb8fda934899afe692f45a38fca04bff2
ms.sourcegitcommit: fdaad48994bdb9e35cdd445c31b4bac0dd006294
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85414036"
---
# <a name="read-text-from-images-and-documents"></a>Szöveg olvasása képekből és dokumentumokból

A Computer Vision olyan új, mélyreható, optikai karakterfelismerési (OCR) képességeket tartalmaz, amelyek kinyerik a nyomtatott vagy a kézírásos szöveget a képekből és a PDF-dokumentumokból. Computer Vision kinyeri az analóg dokumentumokból (képekből, beolvasott dokumentumokból) és a digitalizált dokumentumokból származó szöveget. Szöveget is kinyerheti a képekből a vadonban, például a lemezekről vagy a sorozatszámokkal rendelkező tárolók, valamint a dokumentumok – számlák, számlák, pénzügyi jelentések, cikkek és egyebek között. Ez az OCR funkció a felhőben vagy a helyszínen (tárolókban) felügyelt szolgáltatás részeként érhető el. Emellett támogatja a virtuális hálózatokat és a magánhálózati végpontokat a vállalati szintű megfelelőségi és adatvédelmi igények kielégítése érdekében.

## <a name="read-api"></a>API olvasása 

A Computer Vision [READ API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) a Microsoft legújabb OCR-technológiája, amely a képekből és PDF-dokumentumokból származó kinyomtatott szöveget, kézírásos szöveget (csak angol nyelven), számjegyeket és pénznem szimbólumokat gyűjti. A rendszer úgy van optimalizálva, hogy szövegeket kinyerje a-Wild, a Visual noiset tartalmazó képekből, a digitális vagy a beolvasott PDF-dokumentumokból, valamint a nagy szövegű képekből. Támogatja a nyomtatott és a kézírásos szöveg (angol) és a vegyes nyelvek használatát ugyanabban a képen vagy dokumentumban. A támogatott nyelvek teljes listája megtalálható a [nyelvi támogatásban Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#text-recognition) oldalon.


### <a name="how-it-works"></a>Működés

Az [OLVASÁSI API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) aszinkron. Az első lépés az olvasási művelet meghívása. Az olvasási művelet egy képet vagy PDF-dokumentumot vesz fel bemenetként, és visszaadja a művelet AZONOSÍTÓját. 

A második lépés az [eredmények lekérése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750) művelet meghívása. Ez a művelet a művelet AZONOSÍTÓját veszi át, amelyet az olvasási művelet hozott létre. Ezután visszaadja a kinyert szöveges tartalmat a rendszerképből vagy a dokumentumból JSON formájában. A JSON-válasz a felismert szavak eredeti sorát tárolja. Magában foglalja a kinyert szöveg sorait és a hozzájuk tartozó mezők koordinátáit. Minden szöveges sor tartalmazza az összes kinyert szót a koordinátáikkal és a megbízhatósági pontszámokkal.

Ha szükséges, olvassa el a felismert lap rotációs eltolásának kiírása a következő ábrán látható módon, a vízszintes képtengelyre vonatkozó fokban megadva.

![Egy elforgatott kép és annak szövege olvasható és körülhatárolt](./Images/vision-overview-ocr-read.png)

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

Az olvasási API támogatja a nyomtatott szövegek angol, spanyol, német, francia, olasz, portugál és holland nyelvű kinyerését. Ha a forgatókönyv további nyelvek támogatását igényli, tekintse meg a jelen dokumentum OCR API áttekintése című témakörét. Tekintse meg az összes [támogatott nyelv](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#text-recognition) listáját

![Egy elforgatott kép és annak szövege olvasható és körülhatárolt](./Images/supported-languages-example.png)

### <a name="mixed-languages-support"></a>Vegyes nyelvek támogatása

Az olvasási API támogatja a több nyelvet tartalmazó képeket és dokumentumokat, amelyek gyakran más néven vegyes nyelvi dokumentumok. Ezt úgy végezheti el, hogy a szöveg tartalmának kibontása előtt osztályozza a dokumentumban lévő egyes szöveges vonalakat az észlelt nyelvre.

![Egy elforgatott kép és annak szövege olvasható és körülhatárolt](./Images/mixed-language-example.png)

### <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

Akárcsak az összes kognitív szolgáltatás esetében, az olvasási szolgáltatást használó fejlesztőknek ismerniük kell a Microsoft-szabályzatokat az ügyféladatok alapján. További információért tekintse meg a [Microsoft adatvédelmi Központjának](https://www.microsoft.com/en-us/trust-center/product-overview) Cognitive Services lapját.

### <a name="deploy-on-premises"></a>Helyszíni üzembe helyezés

Az olvasás a Docker-tárolóként (előzetes verzió) is elérhető, amely lehetővé teszi az új OCR-képességek üzembe helyezését a saját környezetében. A tárolók az adott biztonsági és adatirányítási követelményekhez is kiválóak. Lásd: [olvasási tárolók telepítése és futtatása.](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)


## <a name="ocr-api"></a>OCR API

Az [OCR API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) egy régebbi felismerési modellt használ. Csak egyetlen lemezképet támogat, nem PDF-fájlokat, és azonnali választ ad vissza. [Több nyelvet](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#text-recognition) támogat, mint az olvasási API.

## <a name="next-steps"></a>További lépések

- További információ az [olvasási 3,0 Rest APIról](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005).
- A [szöveg kinyerése](./QuickStarts/CSharp-hand-text.md) rövid útmutatóval a C#, a Java, a JavaScript vagy a Python használatával, valamint a REST API segítségével implementálhatja az OCR-t.
