---
title: Egyéni kiejtés használata egyéni beszéd szolgáltatás az Azure-on |} Microsoft Docs
description: Megtudhatja, hogyan nyelvi modell létrehozása kognitív szolgáltatások egyéni beszéd szolgáltatással.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 11/23/2017
ms.author: panosper
ms.openlocfilehash: a74b69b84cc80809a25f18b580a18abb5721b8b1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347074"
---
# <a name="enable-custom-pronunciation"></a>Egyéni kiejtés engedélyezése
Egyéni kiejtés lehetővé teszi, hogy a felhasználók fonetikus űrlap és egy szót vagy kifejezést megjelenítését. Akkor célszerű testreszabott feltételek, például a termék nevét vagy a mozaikszavak kezelése. Szüksége egy kiejtés fájlt (egy egyszerű .txt fájlt).

Ez annak működéséről. Egy egyetlen .txt fájlban több egyéni kiejtés bejegyzést adhat meg. A struktúra a következőképpen történik:

```
Display form <Tab> Spoken form <Newline>
```

*Példák*:

| Űrlap megjelenítése | Szóbeli képernyő |
|----------|-------|
| C3PO | három pea o lásd: |
| L8R | késői vannak. |
| CNTK | n tea k lásd:|

## <a name="requirements-for-the-spoken-form"></a>A szóbeli űrlap követelményei
A szóbeli űrlap kisbetűnek kell lennie, amely kényszeríthető az importálás során. Emellett meg kell adnia az adatok importáló ellenőrzései. A szóbeli és az űrlap megjelenítése vagy lapján nem megengedett. Lehetséges, azonban, több lehet tiltott karakter az űrlap megjelenítése (például ~ és ^).

Minden egyes .txt fájl rendelkezhet több bejegyzést. Például tekintse meg az alábbi képernyőfelvételen:

![Képernyőfelvétel a Jegyzettömb tartozó mozaikszó kiejtés több bejegyzések](../../../media/cognitive-services/custom-speech-service/custom-speech-pronunciation-file.png)

A szóbeli érték az űrlap megjelenítése fonetikus sorrendjét. Betűket, szavakat vagy szótagokat határoznak áll. Jelenleg nincs további útmutatást vagy set szabványok segítséget nyújtanak a szóbeli űrlap állítson össze. 

## <a name="supported-pronunciation-characters"></a>Támogatott kiejtés karakter
Egyéni kiejtés jelenleg (en-US) angol és német (de-de) támogatott. A karakterkészlet (fájlban az egyéni kiejtés) a kifejezés a szóbeli űrlap Express használható a következő táblázatban látható: 

| Nyelv | Karakterek |
|---------- |----------|
| Angol (en-US) | a, b, c, d, e, f, g, h, i, j, k, l, o, p, q, r, s, t, u, v, w, x, y, z |
| Német (de-de) | ä, lumen, ü, ẞ, a, b, c, d, e, f, g, h, i, j, k, l, o, p, q, r, s, t, u, v, w, x, y, z |

>[MEGJEGYZÉS] Egy kifejezés űrlap megjelenítése (fájlban kiejtés) kell megírni ugyanúgy nyelvi kiigazítása adatkészlet.

## <a name="requirements-for-the-display-form"></a>Az űrlap megjelenítése követelményei
Egy űrlap megjelenítése nem adható meg, egy egyéni szó, kifejezés, mozaikszó vagy összetett szavak, amelyek a meglévő szavak kombinálják. Egyéb kiejtés gyakori szavak is megadhat. 

>[!NOTE]
Ezzel a szolgáltatással újraszövegezése bennük gyakori szavak vagy a szóbeli űrlap módosítása nem ajánlott. Érdemes futtatni a dekóder megjelenítéséhez, ha a rendszer nem dekódolni megfelelően néhány szokatlan szavak (például rövidítések, műszaki, és külső szavak). Ha igen, vegye fel őket a egyéni kiejtés fájlba. A nyelvi modell mindig, és csak használjon a szó űrlap megjelenítése. 

## <a name="requirements-for-the-file-size"></a>A fájlméret követelményei
A .txt fájl, amely a kiejtés bejegyzéseket mérete legfeljebb 1 MB. Általában nem kell feltölteni a nagy mennyiségű adat keresztül ezt a fájlt. A legtöbb egyéni kiejtés fájlok valószínűleg csak néhány KB-nál. A .txt fájlt, minden nyelvhez kódolás az UTF-8 AJ kell lennie. Az angol nyelvű területi beállítás esetén ANSI egyben elfogadható.

## <a name="next-steps"></a>További lépések
* Hozzon létre pontosságának javítása a [egyéni akusztikus modell](cognitive-services-custom-speech-create-acoustic-model.md).
* [Hozzon létre egy egyéni beszéd-szöveg végpontot](cognitive-services-custom-speech-create-endpoint.md), amelynek segítségével az alkalmazásokból.
