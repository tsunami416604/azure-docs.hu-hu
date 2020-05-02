---
title: Nyomtatott, kézírásos szöveges felismerés – Computer Vision
titleSuffix: Azure Cognitive Services
description: A nyomtatott és a kézírásos szöveg felismeréséhez kapcsolódó fogalmak a Computer Vision API használatával.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: c829579f3ad9922c8c621b6dbfa966b69a2497d1
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690814"
---
# <a name="recognize-printed-and-handwritten-text"></a>Nyomtatott és kézzel írt szöveg felismerése

Computer Vision számos olyan szolgáltatást biztosít, amely a képeken megjelenő nyomtatott vagy kézírásos szöveg észlelésére és kinyerésére szolgál. Ez számos olyan forgatókönyvben hasznos, mint például a jegyzet készítése, az orvosi nyilvántartások, a biztonság és a banki szolgáltatások. A következő három szakaszban három különböző, különböző használati esetekre optimalizált szöveges felismerési API található.

## <a name="read-api"></a>API olvasása

Az olvasási API a legújabb felismerési modellek használatával észleli a képek szöveges tartalmát, és átalakítja az azonosított szöveget egy géppel olvasható, a gép számára olvasható adatfolyamba. Ez a szöveg-nagy méretű képekre (például a digitálisan ellenőrzött dokumentumokra) és a sok vizuális zajjal rendelkező képekre van optimalizálva. Meghatározza, hogy melyik felismerési modellt kell használni az egyes szövegekhez, valamint a nyomtatott és a kézzel írott szöveggel is alátámasztott képeket. Az olvasási API aszinkron módon hajtható végre, mert a nagyobb dokumentumok több percet is igénybe vehetnek.

Az olvasási művelet megőrzi a felismert szavak eredeti sorát a kimenetében. Az egyes sorokhoz tartozik egy határolókeret koordinátái, és a sorban lévő minden szó saját koordinátákkal is rendelkezik. Ha egy szót alacsony megbízhatósággal ismertek fel, akkor az adatokat is továbbítjuk. További információért tekintse meg az [API 2.0](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) -s verzióra hivatkozó dokumentációt, vagy [olvassa el az API v 3.0 előzetes verziójának dokumentációját](https://go.microsoft.com/fwlink/?linkid=2118322) .

> [!NOTE]
> Ez a funkció csak az angol és a spanyol (v 3.0 előzetes verzió) szövegben érhető el.

### <a name="image-requirements"></a>A rendszerképre vonatkozó követelmények

Az olvasási API az alábbi követelményeknek megfelelő rendszerképekkel működik:

- A képet JPEG, PNG, BMP, PDF vagy TIFF formátumban kell megjeleníteni.
- A rendszerkép méretének 50 x 50 és 10000 x 10000 képpont közé kell esnie. A PDF-lapoknak 17 x 17 hüvelyk vagy kisebbnek kell lenniük.
- A rendszerkép fájlméretének kisebbnek kell lennie 20 megabájtnál (MB).

### <a name="limitations"></a>Korlátozások

Ha ingyenes szintű előfizetést használ, az olvasási API csak egy PDF-vagy TIFF-dokumentum első két oldalát dolgozza fel. Fizetős előfizetéssel akár 200 lapot is feldolgozhat. Azt is vegye figyelembe, hogy az API legfeljebb 300 sort fog érzékelni oldalanként.

## <a name="ocr-optical-character-recognition-api"></a>OCR (optikai karakterfelismerés) API

Computer Vision optikai karakterfelismerési (OCR) API hasonló az olvasási API-hoz, de szinkron módon fut, és nem nagy méretű dokumentumokra van optimalizálva. Egy korábbi felismerési modellt használ, de több nyelven is működik; a támogatott nyelvek teljes listáját a [nyelvi támogatás](language-support.md#text-recognition) részben tekintheti meg.

Ha szükséges, az OCR kijavította a felismert szöveg forgását úgy, hogy a vízszintes képtengelyre vonatkozó fokban adja vissza a rotációs eltolást. Az OCR az egyes szavak keretének koordinátáit is megadja az alábbi ábrán látható módon.

![Egy elforgatott kép és annak szövege olvasható és körülhatárolt](./Images/vision-overview-ocr.png)

További információt az [OCR-dokumentációban](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) talál.

### <a name="image-requirements"></a>A rendszerképre vonatkozó követelmények

Az OCR API olyan rendszerképeken működik, amelyek megfelelnek az alábbi követelményeknek:

* A képet JPEG, PNG, GIF vagy BMP formátumban kell megjeleníteni.
* A bemeneti rendszerkép méretének 50 x 50 és 4200 x 4200 képpont közé kell esnie.
* A képen látható szöveg a 90 fokos többszörösével, valamint egy kisebb, legfeljebb 40 fokos szöggel forgatható el.

### <a name="limitations"></a>Korlátozások

Azokon a fényképeken, ahol a szöveg domináns, a hamis pozitív szavak a részben felismert szavakat eredményezik. Egyes fényképeken, különösen szöveg nélkül, a pontosság a rendszerkép típusától függően változhat.

## <a name="recognize-text-api"></a>szövegfelismerés API

> [!NOTE]
> A szövegfelismerés API az olvasási API mellett elavult. Az olvasási API hasonló képességekkel rendelkezik, és frissül a PDF-, a TIFF-és a többoldalas fájlok kezeléséhez.

A szövegfelismerés API hasonló az OCR-hez, de aszinkron módon fut, és frissített felismerő modelleket használ. További információért tekintse meg az [SZÖVEGFELISMERÉS API-dokumentációt](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) .

### <a name="image-requirements"></a>A rendszerképre vonatkozó követelmények

A szövegfelismerés API a következő követelményeknek megfelelő rendszerképekkel működik:

- A képet JPEG, PNG vagy BMP formátumban kell megjeleníteni.
- A rendszerkép méretének 50 x 50 és 4200 x 4200 képpont közé kell esnie.
- A rendszerkép fájlméretének 4 megabájtnál (MB) kisebbnek kell lennie.

## <a name="limitations"></a>Korlátozások

A szöveges felismerési műveletek pontossága a képek minőségétől függ. A következő tényezők pontatlan olvasást eredményezhetnek:

* Elmosódott képek.
* Kézzel írt, folyóírásos szöveg.
* Művészi betűstílusok.
* Kis méretű szöveg.
* Bonyolult háttér, árnyékok, a szöveget átfedő fényhatás vagy perspektivikus torzítás.
* Túlméretezett vagy hiányzó nagybetűk a szavak elején.
* Felső vagy alsó indexben lévő, vagy áthúzott szöveg.

## <a name="next-steps"></a>További lépések

A szöveges felismerés egyszerű C#-alkalmazásban való megvalósításához kövesse a szöveg [kinyerése (olvasás)](./QuickStarts/CSharp-hand-text.md) rövid útmutatót.
