---
title: Egyéni írásmódja – Custom Speech Service használata
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hozhat létre a Custom Speech Service nyelvi modell.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: conceptual
ms.date: 11/23/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: 6442eec3e622282ecf6a3bf884110cd435fc18be
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47223474"
---
# <a name="enable-custom-pronunciation"></a>Engedélyezze az egyéni kiejtése
Egyéni írásmódja lehetővé teszi a felhasználóknak adható meg fonetikus űrlap és megjelenített egy szó vagy kifejezés. Ez hasznos testre szabott feltételek, például termékneveket vagy betűszavakat kezelése. Szüksége egy írásmódja fájlt (egy egyszerű .txt fájlt).

Itt látható, hogyan működik. Egy egyetlen .txt fájlban a több egyéni írásmódja bejegyzést is megadhatja. A struktúra a következőképpen történik:

```
Display form <Tab> Spoken form <Newline>
```

*Példák*:

| Megjelenítési űrlap | Használja a beszélt űrlap |
|----------|-------|
| C3PO | három csúc o lásd: |
| L8R | késedelmes vannak |
| CNTK | Lásd: n tea k|

## <a name="requirements-for-the-spoken-form"></a>A beszédből kinyert képernyő követelményei
A beszédből kinyert képernyő kisbetűnek kell lennie, az importálás során kényszeríthető. Emellett meg kell adnia az adatok programu Pro import ellenőrzései. Nincs lapján vagy a kimondott képernyő, vagy a megjelenítési űrlap számára engedélyezett. Lehetséges, azonban, több lehet tiltott karakter a megjelenítési űrlap (például ~ és ^).

Minden egyes .txt fájl több bejegyzés is rendelkezhet. Például tekintse meg az alábbi képernyőfelvételen látható:

![Képernyőkép a Jegyzettömb betűszó írásmódja számos bejegyzést a](../../../media/cognitive-services/custom-speech-service/custom-speech-pronunciation-file.png)

A beszédből kinyert képernyő az fonetikus sorrendje a megjelenített képernyő. Betűket, szavakat vagy szótagokat határoznak áll. Jelenleg nincs további útmutatást vagy szabványgyűjtemény állítson össze a kimondott űrlap segítségével. 

## <a name="supported-pronunciation-characters"></a>Támogatott írásmódja karakter
Egyéni írásmódja jelenleg támogatott (en-US) angol és német (de-de). A karakterkészlet, amely egy kifejezés (az egyéni írásmódja fájlban) használja a beszélt formájában express segítségével az alábbi táblázatban látható: 

| Nyelv | Karakterek |
|---------- |----------|
| Angol (en-US) | a, b, c, d, e, f, g, h i, j, ezer, l, o, p, válaszok, r, s, t, u, v, w, x, y, z |
| Német (de-de) | ä lumen, ü, ẞ, a, b, c, d, e, f, g, h i, j, ezer, l, o, p, válaszok, r, s, t, u, v, w, x, y, z |

>[MEGJEGYZÉS] Egy kifejezés megjelenítési űrlap (fájlban írásmódja) nyelven kell megírni ugyanúgy nyelvi betanítás adatkészlet.

## <a name="requirements-for-the-display-form"></a>A megjelenítési űrlap követelményei
Megjelenítési űrlap csak lehet egy egyedi szót, kifejezést, betűszó vagy összetett szavak, amelyek meglévő szavak. A gyakori szavakat egyéb kiejtés is megadhat. 

>[!NOTE]
Ezzel a funkcióval újraszövegezése gyakori szavakat vagy a kimondott képernyő módosítása nem ajánlott. Célszerűbb a dekóder megtekintheti, ha egyes ritka szavakat (például rövidítéseket, műszaki szakkifejezéseket és külső szavak) vannak se megfelelően dekódovat futtassa. Ha igen, adja hozzá azokat az egyéni kiejtés fájlhoz. A nyelvi modell csak és mindig használjon a megjelenítési űrlap szó. 

## <a name="requirements-for-the-file-size"></a>A fájl mérete követelményei
Írásmódja bejegyzéseket tartalmazó .txt fájl mérete legfeljebb 1 MB. Általában nem kell a nagy mennyiségű adat feltöltése keresztül ezt a fájlt. Egyéni írásmódja fájlok a legtöbb esetben valószínűleg néhány Tudásbázis méretű lehet. Az összes területi beállításhoz tartozó .txt fájl kódolása az UTF-8 AJ kell lennie. Az angol területi beállítás, az ANSI is fogadható el.

## <a name="next-steps"></a>További lépések
* Hatékonyabbá pontosságát a [importálni akusztikai modell](cognitive-services-custom-speech-create-acoustic-model.md).
* [Hozzon létre egy egyéni hang-szöveg transzformációs végpontot](cognitive-services-custom-speech-create-endpoint.md), amelyet használhat az alkalmazásokból.
