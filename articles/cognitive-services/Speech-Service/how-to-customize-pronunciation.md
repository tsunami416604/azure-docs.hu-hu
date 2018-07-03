---
title: Az Azure Cognitive Services beszédszolgáltatás |} A Microsoft Docs
description: Ismerje meg, hogyan szabhatja testre a beszédfelismerési szolgáltatás Cognitive Services szolgáltatással írásmódja.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 07/02/2018
ms.author: panosper
ms.openlocfilehash: f926bf8686a279c93d8f1ed03e8a0ecf3f4a9632
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2018
ms.locfileid: "37347403"
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

![Képernyőkép a Jegyzettömb betűszó írásmódja számos bejegyzést a](media/stt/custom-speech-pronunciation-file.png)

A beszédből kinyert képernyő az fonetikus sorrendje a megjelenített képernyő. Betűket, szavakat vagy szótagokat határoznak áll. Jelenleg nincs további útmutatást vagy szabványgyűjtemény állítson össze a kimondott űrlap segítségével. 

## <a name="supported-pronunciation-characters"></a>Támogatott írásmódja karakter
Egyéni írásmódja jelenleg támogatott (en-US) angol és német (de-de). A karakterkészlet, amely egy kifejezés (az egyéni írásmódja fájlban) használja a beszélt formájában express segítségével az alábbi táblázatban látható: 

| Nyelv | Karakterek |
|---------- |----------|
| Angol (en-US) | a, b, c, d, e, f, g, h i, j, ezer, l, o, p, válaszok, r, s, t, u, v, w, x, y, z |
| Német (de-de) | ä lumen, ü, ẞ, a, b, c, d, e, f, g, h i, j, ezer, l, o, p, válaszok, r, s, t, u, v, w, x, y, z |

> [!NOTE]
> Egy kifejezés megjelenítési űrlap (fájlban írásmódja) nyelven kell megírni ugyanúgy nyelvi betanítás adatkészlet.

## <a name="requirements-for-the-display-form"></a>A megjelenítési űrlap követelményei
Megjelenítési űrlap csak lehet egy egyedi szót, kifejezést, betűszó vagy összetett szavak, amelyek meglévő szavak. A gyakori szavakat egyéb kiejtés is megadhat. 

>[!NOTE]
>Ezzel a funkcióval újraszövegezése gyakori szavakat vagy a kimondott képernyő módosítása nem ajánlott. Célszerűbb a dekóder megtekintheti, ha egyes ritka szavakat (például rövidítéseket, műszaki szakkifejezéseket és külső szavak) vannak se megfelelően dekódovat futtassa. Ha igen, adja hozzá azokat az egyéni kiejtés fájlhoz. A nyelvi modell csak és mindig használjon a megjelenítési űrlap szó. 

## <a name="requirements-for-the-file-size"></a>A fájl mérete követelményei
Írásmódja bejegyzéseket tartalmazó .txt fájl mérete legfeljebb 1 MB. Általában nem kell a nagy mennyiségű adat feltöltése keresztül ezt a fájlt. Egyéni írásmódja fájlok a legtöbb esetben valószínűleg néhány Tudásbázis méretű lehet. Az összes területi beállításhoz tartozó .txt fájl kódolása az UTF-8 AJ kell lennie. Az angol területi beállítás, az ANSI is fogadható el.

## <a name="next-steps"></a>További lépések
* Pontosságának javítása hozzon létre egy [importálni akusztikai modell](how-to-customize-acoustic-models.md)
* Pontosságának javítása hozzon létre egy [egyéni nyelvi modell](how-to-customize-language-model.md)
