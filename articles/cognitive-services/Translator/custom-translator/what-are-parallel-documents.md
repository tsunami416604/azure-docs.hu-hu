---
title: Mik azok a párhuzamos dokumentumok? -Egyéni fordító
titleSuffix: Azure Cognitive Services
description: A párhuzamos dokumentumok olyan dokumentumok, amelyekben az egyik a másik fordítása. A pár egyik dokumentuma mondatokat tartalmaz a forrás nyelvén, a másik dokumentum pedig ezeket a mondatokat a célként megadott nyelvre lefordítva tartalmazza.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 6477432636db9592d9b0d9c1f90f5eee7d6e00db
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369001"
---
# <a name="what-are-parallel-documents"></a>Mik azok a párhuzamos dokumentumok?

A párhuzamos dokumentumok olyan dokumentumok, amelyekben az egyik a másik fordítása. A pár egyik dokumentuma mondatokat tartalmaz a forrás nyelvén, a másik dokumentum pedig ezeket a mondatokat a célként megadott nyelvre lefordítva tartalmazza.
Nem számít, hogy melyik nyelven van megjelölve a "forrás", és hogy melyik nyelven van megjelölve "Target" – párhuzamos dokumentum, amely mindkét irányban betanítható a fordítási rendszerek.

## <a name="requirements"></a>Követelmények

A rendszer betanításához legalább 10 000 egyedi, egymásra igazított párhuzamos mondatra lesz szüksége. Ez a korlátozás egy biztonsági háló, amely biztosítja, hogy a párhuzamos mondatok elég egyedi szókincset tartalmazzanak a fordítási modell sikeres betanításához. Ajánlott eljárásként a fordítási rendszer minőségének javításához folyamatosan vegyen fel több párhuzamos tartalmat és újratanítást. Tekintse meg a [mondatok igazítását](./sentence-alignment.md).

A Microsoft megköveteli, hogy az egyéni fordítónak feltöltött dokumentumok ne sértsék harmadik fél szerzői jogi vagy szellemi tulajdonságait. További információkért tekintse meg a használati [feltételeket](https://azure.microsoft.com/support/legal/cognitive-services-terms/).
A dokumentum a portál használatával történő feltöltése nem módosítja a szellemi tulajdon tulajdonjogát a dokumentumban.

## <a name="use-of-parallel-documents"></a>Párhuzamos dokumentumok használata

A párhuzamos dokumentumokat a rendszer használja:

1.  Ha szeretné megtudni, hogy a szavak, kifejezések és mondatok milyen gyakran vannak leképezve a két nyelv között.

2.  Megtudhatja, hogyan dolgozza fel a megfelelő környezetet a környező kifejezésektől függően. Előfordulhat, hogy a szó nem mindig ugyanaz a szó a másik nyelven.

Ajánlott eljárásként ellenőrizze, hogy van-e 1:1-mondat a dokumentumok forrás és cél nyelvi verziói között.

Ha a projekt tartományi (kategória-) specifikus, a dokumentumoknak konzisztensnek kell lenniük a kategórián belüli terminológiában. Az eredményül kapott fordítási rendszer minősége a dokumentumok készletében lévő mondatok számától és a mondatok minőségétől függ. Minél több példát tartalmaz a dokumentumok különböző, a kategóriára jellemző kifejezésekkel való használatra, annál jobb feladat lehet a rendszer a fordítás során.

A feltöltött dokumentumok magánjellegűek az egyes munkaterületeken, és tetszőleges számú projektben vagy képzésben használhatók. A dokumentumokból kinyert mondatokat a tárházban külön tároljuk egyszerű Unicode szövegfájlként, és elérhetők a törléshez. Ne használja az egyéni fordítót dokumentum-adattárként, nem fogja tudni letölteni a feltöltött dokumentumokat a feltöltött formátumban.



## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan használhat [szótárt](what-is-dictionary.md) az egyéni fordítóban.