---
title: Nyomtatott, kézzel írt szövegfelismerés - Computer Vision
titleSuffix: Azure Cognitive Services
description: A képeknyomtatott és kézzel írt szövegének a Computer Vision API-val történő felismerésére vonatkozó fogalmak.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: a4c90ed12c8023e0b9ebc509b20d8d9224b49f1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220159"
---
# <a name="recognize-printed-and-handwritten-text"></a>Nyomtatott és kézzel írt szöveg felismerése

A Computer Vision számos olyan szolgáltatást nyújt, amely észleli és kinyeri a képeken megjelenő nyomtatott vagy kézzel írt szöveget. Ez számos esetben hasznos, például a jegyzetelés, az orvosi feljegyzések, a biztonság és a banki szolgáltatások. A következő három szakasz három különböző szövegfelismerési API-t részletez, amelyek mindegyike különböző használati esetekre van optimalizálva.

## <a name="read-api"></a>API olvasása

A Read API észleli a kép szöveges tartalmát a legújabb felismerési modelljeinkkel, és az azonosított szöveget géppel olvasható karakterfolyammá alakítja. A szöveggel kapcsolatos képekre (például a digitálisan beolvasott dokumentumokra) és a sok vizuális zajt tartalmazó képekre van optimalizálva. Meghatározza, hogy melyik felismerési modellt használja az egyes szövegsorokhoz, és támogatja a nyomtatott és kézzel írt szöveget is tartalmazó képeket. A Read API aszinkron módon hajtvégre, mert a nagyobb dokumentumok több percet is igénybe vehetaz eredmény visszaadása.

Az Olvasás művelet a kimenetben megtartja a felismert szavak eredeti sorcsoportjait. Minden sor hoz határolókeret-koordinátákat, és a soron belül minden szónak saját koordinátái is vannak. Ha egy szót alacsony megbízhatósággal ismertek fel, akkor ez az információ is továbbításra kerül. További információkért tekintse meg az [API-referenciadokumentumok olvasása](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) és [az API előzetes verzióhivatkozási dokumentumok](https://go.microsoft.com/fwlink/?linkid=2118322) című dokumentumokat.

> [!NOTE]
> Ez a funkció csak angol és spanyol (előzetes verziójú) szövegek esetén érhető el.

### <a name="image-requirements"></a>A rendszerképre vonatkozó követelmények

A Read API olyan lemezképekkel működik, amelyek megfelelnek az alábbi követelményeknek:

- A képet JPEG, PNG, BMP, PDF vagy TIFF formátumban kell megjelenni.
- A kép méretének 50 x 50 és 10000 x 10000 képpont között kell lennie. A PDF-oldalaknak legalább 17 x 17 hüvelyknek kell lenniük.
- A kép fájlméretének 20 megabájtnál (MB) kisebbnek kell lennie.

### <a name="limitations"></a>Korlátozások

Ha ingyenes szintű előfizetést használ, az Olvasási API csak a PDF- vagy TIFF-dokumentum első két oldalát dolgozza fel. Fizetős előfizetéssel akár 200 oldalt is feldolgozhat. Azt is vegye figyelembe, hogy az API oldalanként legfeljebb 300 sort észlel.

## <a name="ocr-optical-character-recognition-api"></a>OCR (optikai karakterfelismerés) API

A Computer Vision optikai karakterfelismerő (OCR) API-ja hasonló az Olvasási API-hoz, de szinkron módon hajtható végre, és nincs nagy dokumentumokra optimalizálva. Egy korábbi felismerési modellt használ, de több nyelvvel működik; a támogatott nyelvek teljes listájának [nyelvi támogatása.](language-support.md#text-recognition)

Szükség esetén az OCR úgy javítja ki a felismert szöveg elforgatását, hogy a vízszintes képtengely körül fokokban visszaadja az elforgatáseltolást. Az OCR az egyes szavak keretkoordinátáit is megadja, ahogy az az alábbi ábrán is látható.

![Elforgatott kép, szövege olvasása és határvonala](./Images/vision-overview-ocr.png)

További információ az [OCR referenciadokumentumaiban.](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc)

### <a name="image-requirements"></a>A rendszerképre vonatkozó követelmények

Az OCR API az alábbi követelményeknek megfelelő lemezképeken működik:

* A képet JPEG, PNG, GIF vagy BMP formátumban kell megjelenni.
* A bemeneti kép méretének 50 x 50 és 4200 x 4200 képpont között kell lennie.
* A kép szövege 90 fokos többszörössel és akár 40 fokos kis szöggel is elforgatható.

### <a name="limitations"></a>Korlátozások

Azokon a fényképeken, ahol a szöveg dominál, a hamis pozitív umok részben felismert szavakból származhatnak. Egyes fényképeken, különösen a szöveg nélküli fényképeken a pontosság a kép típusától függően változhat.

## <a name="recognize-text-api"></a>Szöveg felismerése API

> [!NOTE]
> A Szöveg felismerése API elavult a Read API javára. Az Olvasási API hasonló képességekkel rendelkezik, és a PDF- és TIFF-fájlok kezeléséhez frissül.

A Szöveg felismerése API hasonló az OCR-hez, de aszinkron módon hajtja végre, és frissített felismerési modelleket használ. További információért tekintse meg a [Szöveg felismerése API-referencia-dokumentumait.](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200)

### <a name="image-requirements"></a>A rendszerképre vonatkozó követelmények

A Szöveg felismerése API az alábbi követelményeknek megfelelő lemezképekkel működik:

- A képet JPEG, PNG vagy BMP formátumban kell megjelenni.
- A kép méretének 50 x 50 és 4200 x 4200 képpont között kell lennie.
- A kép fájlméretének 4 megabájtnál (MB) kisebbnek kell lennie.

## <a name="limitations"></a>Korlátozások

A szövegfelismerési műveletek pontossága a képek minőségétől függ. A következő tényezők pontatlan leolvasást okozhatnak:

* Elmosódott képek.
* Kézzel írt, folyóírásos szöveg.
* Művészi betűstílusok.
* Kis méretű szöveg.
* Bonyolult háttér, árnyékok, a szöveget átfedő fényhatás vagy perspektivikus torzítás.
* Túlméretezett vagy hiányzó nagybetűk a szavak elején.
* Felső vagy alsó indexben lévő, vagy áthúzott szöveg.

## <a name="next-steps"></a>További lépések

Kövesse a [Szöveg kibontása (Olvasás)](./QuickStarts/CSharp-hand-text.md) rövid útmutató t a szövegfelismerés megvalósításához egy egyszerű C# alkalmazásban.
