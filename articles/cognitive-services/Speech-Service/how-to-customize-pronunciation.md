---
title: Testre szabhatja a írásmódja – beszédszolgáltatások
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogyan szabhatja testre a Speech szolgáltatással írásmódja. Az egyéni írásmódja meghatározhatja fonetikus formában és megjelenített egy szó vagy kifejezés. Ez hasznos testre szabott feltételek, például termékneveket vagy betűszavakat kezelése. A kezdéshez szüksége egy írásmódja fájlnevével – egy egyszerű .txt fájlt.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 0a3dfce10fc8ea76bc8f99e2459295bc637017dc
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878408"
---
# <a name="enable-custom-pronunciation"></a>Engedélyezze az egyéni kiejtése

Egyéni írásmódja használatával meghatározhatja a fonetikus formában és megjelenített egy szó vagy kifejezés. Ez hasznos testre szabott feltételek, például termékneveket vagy betűszavakat kezelése. A kezdéshez szüksége egy írásmódja fájlnevével – egy egyszerű .txt fájlt.

Itt látható, hogyan működik. Egy egyetlen .txt fájlban a több egyéni írásmódja bejegyzést is megadhatja. A struktúra a következőképpen történik:

```
Display form <Tab> Spoken form <Newline>
```

Az alábbi táblázatban néhány példa látható:

| Megjelenítési űrlap | Használja a beszélt űrlap |
|----------|-------|
| C3PO | három csúc o lásd: |
| L8R | késedelmes vannak |
| CNTK | Lásd: n tea k|

## <a name="requirements-for-the-spoken-form"></a>A beszédből kinyert képernyő követelményei
A beszédből kinyert képernyő kisbetűnek kell lennie, amely kényszerítheti, hogy az importálás során. Meg kell adnia az adatok programu Pro import ellenőrzéseket is. Nincs lapján vagy a kimondott képernyő, vagy a megjelenítési űrlap számára engedélyezett. Azonban van Előfordulhat, hogy lehet több tiltott karaktereket a megjelenítési űrlap (például ~ és ^).

Minden egyes .txt fájl rendelkezhet több bejegyzés a következő képen látható módon:

![Példák a betűszó kiejtése](media/stt/custom-speech-pronunciation-file.png)

A beszédből kinyert képernyő az fonetikus sorrendje a megjelenített képernyő. Betűket, szavakat vagy szótagokat határoznak áll. Jelenleg nincs további útmutatást vagy szabványgyűjtemény állítson össze a kimondott űrlap segítségével.

## <a name="supported-pronunciation-characters"></a>Támogatott írásmódja karakter
Egyéni írásmódja jelenleg támogatott (en-US) angol és német (de-de). A karakterkészlet, amellyel a kimondott képernyőt a kifejezés (az egyéni írásmódja fájlt) expressz az alábbi táblázatban láthatók:

| Nyelv | Karakterek |
|---------- |----------|
| Angol (en-US) | a, b, c, d, e, f, g, h i, j, ezer, l, o, p, válaszok, r, s, t, u, v, w, x, y, z |
| Német (de-de) | ä lumen, ü,?, a, b, c, d, e, f, g, h i, j, ezer, l, o, p, válaszok, r, s, t, u, v, w, x, y, z |

> [!NOTE]
> Egy kifejezés megjelenítési űrlap (fájlban írásmódja) nyelven kell megírni ugyanúgy nyelvi betanítás adatkészlet.

## <a name="requirements-for-the-display-form"></a>A megjelenítési űrlap követelményei
Megjelenítési űrlap csak egy egyéni szó, kifejezés, egy betűszó vagy összetett szavak, amelyek meglévő szavak is lehet. A gyakori szavakat egyéb kiejtés is megadhat.

>[!NOTE]
>Ezzel a funkcióval újraszövegezése gyakori szavakat vagy a kimondott képernyő módosítása nem ajánlott. Célszerűbb a dekóder megtekintéséhez, hogy egyes ritka szavakat (például rövidítéseket, műszaki szakkifejezéseket vagy külső szavak) helytelenül dekódovat futtassa. Ha igen, adja hozzá azokat az egyéni kiejtés fájlhoz. A nyelvi modell csak és mindig használjon a megjelenítési űrlap szó.

## <a name="requirements-for-the-file-size"></a>A fájl mérete követelményei
A .txt fájlt, amely tartalmazza a írásmódja bejegyzések mérete legfeljebb 1 MB (ingyenes szint kulcsok 1KB). Általában nem szükséges nagy mennyiségű adat feltöltése keresztül ezt a fájlt. A legtöbb egyéni írásmódja fájlok mérete valószínűleg csak néhány kilobájtban (KB). Az összes területi beállításhoz tartozó .txt fájl kódolása az UTF-8 AJ kell lennie. Az angol területi beállítás, az ANSI is fogadható el.

## <a name="next-steps"></a>További lépések
* Pontosságának javítása hozzon létre egy [importálni akusztikai modell](how-to-customize-acoustic-models.md).
* Pontosságának javítása hozzon létre egy [egyéni nyelvi modell](how-to-customize-language-model.md).
