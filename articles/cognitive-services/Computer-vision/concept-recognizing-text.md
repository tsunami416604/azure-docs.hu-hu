---
title: Nyomtatott, kézzel írt szöveg – a Computer Vision felismerése
titleSuffix: Azure Cognitive Services
description: A Computer Vision API használatával képeken nyomtatott és kézzel írt szöveg felismerése kapcsolatos fogalmakat.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ea5084128848db7d799b1791aef528305b4f6df4
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57192367"
---
# <a name="recognize-printed-and-handwritten-text"></a>Nyomtatott és kézzel írt szöveg felismerése

Számítógépes Látástechnológia számos olyan szolgáltatások észlelését, és nyomtatott vagy a kézírásos rendszerképek megjelenő szöveg kinyerése. Ez hasznos jegyzetelő, orvosi, biztonsági és banki – egyéb felhasználási helyzetek különböző. A következő három szakasz részletes három különböző szövegek felismerése API-k, minden egyes optimalizált különböző használati helyzetekhez.

## <a name="read-api"></a>Elolvashatják az API

A elolvashatják az API segítségével azonosíthatja a legújabb modelleket a szöveges tartalom észleli és karaktersorozattá alakítja át a meghatározott szöveget. Szöveges adatot használó rendszerképek (például a digitális a beolvasott dokumentumokat) és a képek vizuális zaj rengeteg optimalizálva. Mert adja vissza az eredményt több percet is igénybe vehet a nagyobb dokumentumok aszinkron módon végrehajtja.

Az olvasási művelet fenntartja a felismerhető szavak kimenetét eredeti sor csoportosítását. A soron belül minden szó a saját koordináták is rendelkezik, és az egyes sorok határolókeret koordinátái tartalmaz. Egy szó alacsony magabiztosan ismerhető fel, ha ezt az információt, valamint továbbítani. Tekintse meg a [elolvashatják az API-referenciadokumentumok](https://westus.api.cognitive.microosft.com/vision/v2.0/read/core/asyncBatchAnalyze) további.

> [!NOTE]
> Ez a funkció jelenleg előzetes verzióban és csak angol nyelvre érhető el.

### <a name="image-requirements"></a>A rendszerképre vonatkozó követelmények

Az olvasási API együttműködik a rendszerkép található, az alábbi követelményeknek:

- A kép JPEG, PNG, BMP, PDF vagy TIFF formátumban kell bemutatni.
- A kép mérete 50 x 50 és 4200 x 4200 képpont között kell lennie. PDF-oldalak 17 x 17 hüvelyk vagy kisebb kell lennie.
- A lemezkép mérete kisebb, mint 20 megabájt (MB) kell lennie.

### <a name="limitations"></a>Korlátozások

Ha egy ingyenes szintű előfizetést használ, a elolvashatják az API csak dolgozza fel a PDF- vagy TIFF dokumentum első két oldalát. Fizetős előfizetéssel akár 200 lapokat fogja feldolgozni. Azt is vegye figyelembe, hogy az API-t észlelni fogja legfeljebb 300 sorok egy-egy lapon.

## <a name="ocr-optical-character-recognition-api"></a>OCR (optikai karakterfelismerés) API

Computer Vision API-t optikai karakterfelismerés (OCR) olvassa el az API-hoz hasonló, de végrehajtása szinkron módon történik, és a nagy dokumentumok nincs optimalizálva. Egy korábbi modell működését azonban több nyelvet használja.

Optikai Karakterfelismerés 25 nyelveket támogatja: Arab, kínai (egyszerűsített), kínai (hagyományos), Cseh, dán, holland, angol nyelven, finn, francia, német, görög, magyar, olasz, japán, koreai, norvég, lengyel, portugál, román, spanyol, szerb (cirill betűs és Latin), szlovák, spanyol, Svéd és török. Optikai Karakterfelismerés automatikusan észleli az észlelt szöveg nyelvét.

Ha szükséges, optikai Karakterfelismerés visszaadó forgó eltolás a kép vízszintes tengely kapcsolatos fokban kijavítja a elforgatási szögét a felismert szöveget. Optikai Karakterfelismerés is biztosít a keret koordináták minden szó, az alábbi ábrán látható módon.

![Folyamatban van-e forgatni kép ábrázoló diagram és a szöveg, olvassa el és a előrebocsátott](./Images/vision-overview-ocr.png)

Tekintse meg a [OCR referenciadokumentumok](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) további.

### <a name="image-requirements"></a>A rendszerképre vonatkozó követelmények

Az optikai Karakterfelismerés API működik a lemezképek, amelyek megfelelnek a következő követelményeknek:

* A kép JPEG, PNG, GIF és BMP formátumban kell bemutatni.
* A bemeneti kép mérete 50 x 50 és 4200 x 4200 képpont között kell lennie.
* A kép szövegének forgatható szerint bármilyen több 90 fok és a egy kis szöge legfeljebb 40 fok.

### <a name="limitations"></a>Korlátozások

A fényképek, ahol szövege a következő meghatározó téves részlegesen felismert származhatnak. Az egyes fényképek, különösen a fényképek nélkül szöveg, a pontosság kép típusa függően változhat.

## <a name="recognize-text-api"></a>API szövegének felismerése

> [!NOTE]
> A felismerni API értéke a elolvashatják az API elavult. Az olvasási API hasonló képességekkel rendelkezik, és frissül, és PDF-, TIFF és többoldalas fájlok kezeléséhez.

A felismerni API OCR hasonló, de aszinkron, és a frissített modelleket használ. Tekintse meg a [ismeri fel a szöveges API-referenciadokumentumok](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) további.

### <a name="image-requirements"></a>A rendszerképre vonatkozó követelmények

A felismerni API együttműködik a lemezképek, amelyek megfelelnek a következő követelményeknek:

- A kép JPEG, PNG vagy BMP formátumban kell bemutatni.
- A kép mérete 50 x 50 és 4200 x 4200 képpont között kell lennie.
- A lemezkép mérete kisebb, mint 4 megabájt (MB) kell lennie.

## <a name="improve-results"></a>Hatékonyabb keresés

A szöveg felismerése műveletek pontosságát a képek minőségét függ. Az alábbi tényezők okozhatják egy pontatlan olvasása:

* Elmosódott képek.
* Kézzel írt, folyóírásos szöveg.
* Művészi betűstílusok.
* Kis méretű szöveg.
* Bonyolult háttér, árnyékok, a szöveget átfedő fényhatás vagy perspektivikus torzítás.
* Hiányzik vagy túl nagy méretű nagybetűk, szavakat alapjait.
* Felső vagy alsó indexben lévő, vagy áthúzott szöveg.

## <a name="next-steps"></a>További lépések

Kövesse a [(OCR) nyomtatott szöveg kinyerése](./quickstarts/csharp-print-text.md) rövid útmutató egy egyszerű szövegek felismerése implementálása C# alkalmazást.