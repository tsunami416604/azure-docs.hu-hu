---
title: Mik azok a párhuzamos dokumentumok? -Egyéni a fordítót
titleSuffix: Azure Cognitive Services
description: A párhuzamos dokumentumok olyan dokumentumok, amelyekben az egyik a másik fordítása. A pár egyik dokumentuma mondatokat tartalmaz a forrás nyelvén, a másik dokumentum pedig ezeket a mondatokat a célként megadott nyelvre lefordítva tartalmazza.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: fb54df2e1eb89d30e62ae80355635356343994ee
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595450"
---
# <a name="what-are-parallel-documents"></a>Mik azok a párhuzamos dokumentumok?

A párhuzamos dokumentumok olyan dokumentumok, amelyekben az egyik a másik fordítása. A pár egyik dokumentuma mondatokat tartalmaz a forrás nyelvén, a másik dokumentum pedig ezeket a mondatokat a célként megadott nyelvre lefordítva tartalmazza.
Nem számít, hogy melyik nyelven van megjelölve a "forrás", és hogy melyik nyelven van megjelölve "Target" – párhuzamos dokumentum, amely mindkét irányban betanítható a fordítási rendszerek.

## <a name="requirements"></a>Követelmények

A rendszer betanításához legalább 10 000 egyedi párhuzamos mondatra lesz szükség. Az ajánlott eljárás az, hogy a fordítási rendszer minőségének javítása érdekében folyamatosan bővítse a párhuzamos tartalmakat és az újraképzést.

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

- Megtudhatja, hogyan [](what-is-dictionary.md) használhat szótárt az egyéni fordítóban.
