---
title: Mi az a munkaterület és projekt? - Egyéni fordító
titleSuffix: Azure Cognitive Services
description: Ez a cikk ismerteti a munkaterület és a projekt közötti különbségeket, valamint az egyéni fordító szolgáltatás projektkategóriáit és címkéit.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: d2f7903fa85c645357e46a753d1cb043e0893254
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219433"
---
# <a name="what-is-a-custom-translator-workspace"></a>Mi az egyéni fordító munkaterület?

A munkaterület az egyéni fordítási rendszer összeállításához és létrehozásához szükséges munkaterület. Egy munkaterület több projektet, modellt és dokumentumot is tartalmazhat. Az egyéni fordítóban végzett összes munka egy adott munkaterületen belül van.

A munkaterület magánjellegű az Ön és a munkaterületre meghívott személyek számára. A hívatlan személyek nem férnek hozzá a munkaterület tartalmához. Tetszőleges embereket hívhat meg a munkaterületre, és bármikor módosíthatja vagy eltávolíthatja a hozzáférést. Új munkaterületet is létrehozhat. Alapértelmezés szerint a munkaterület nem tartalmaz olyan projekteket vagy dokumentumokat, amelyek a többi munkaterületen belül vannak.

## <a name="what-is-a-custom-translator-project"></a>Mi az egyéni fordító projekt?

A projekt egy modell, dokumentumok és tesztek burkolója. Minden projekt automatikusan tartalmazza az adott munkaterületre feltöltött összes dokumentumot, amely a megfelelő nyelvi párral rendelkezik. Ha például angolról spanyolra és spanyolról angolra szóló projekttel is rendelkezik, akkor mindkét projektben ugyanazok a dokumentumok lesznek beillesztve. Minden projekthez tartozik egy CategoryID, amely a [V3 API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) fordítások lekérdezésekéneknél használatos. A CategoryID paraméter az egyéni fordítóval készített testreszabott rendszer fordításának lekérni.

## <a name="project-categories"></a>Projektkategóriák

A kategória azonosítja a projekthez tartozó tartományt – a használni kívánt terminológiai és stílusterületet. Válassza ki a dokumentumokszempontjából leginkább releváns kategóriát. Bizonyos esetekben a kategória kiválasztása közvetlenül befolyásolja az egyéni fordító viselkedését.

Két alapmodellünk van. Ők általános és technológiai. Ha a **Technológia** kategóriát választja, a technológia alapmodelleket fogja használni. Bármely más kategóriaválasztáshoz az Általános alapmodelleket kell használni. A technológia alapmodell jól működik a technológia területén, de gyengébb minőséget mutat, ha a fordításhoz használt mondatok nem tartoznak a technológiai tartományba. Azt javasoljuk az ügyfeleknek, hogy csak akkor válasszák a technológia kategóriát, ha a mondatok szigorúan a technológiai tartományba tartoznak.

Ugyanebben a munkaterületen különböző kategóriákban hozhat létre projekteket ugyanahhoz a nyelvpárhoz. Egyéni fordító megakadályozza, hogy egy ismétlődő projekt létrehozása az azonos nyelvi pár és kategória. A címkének a projektre történő alkalmazása lehetővé teszi, hogy elkerülje ezt a korlátozást. Ne használjon címkéket, kivéve, ha több ügyfél számára épít fordítási rendszereket, mivel a projekthez egyedi címke hozzáadása tükröződni fog a projektek CategoryID-ben.

## <a name="project-labels"></a>Projektcímkék

Az Egyéni fordító lehetővé teszi, hogy projektcímkét rendeljen a projekthez. A projektcímke megkülönbözteti az azonos nyelvi párt és kategóriával rendelkező projekteket. Ajánlott eljárásként kerülje a projektcímkék használatát, kivéve, ha szükséges.

A projektcímke a CategoryID részeként használatos. Ha a projektcímke nincs beállítva, vagy azonos módon van beállítva a projektek között, akkor az azonos kategóriába tartozó és *különböző* nyelvi párokkal rendelkező projektek ugyanazt a CategoryID azonosítót fogják használni. Ez a megközelítés előnyös, mert lehetővé teszi, hogy Ön vagy az ügyfél a szövegfordító API használatakor váltson a nyelvek között anélkül, hogy az egyes projektekre egyedi CategoryID miatt kellene aggódnia.

Például, ha azt akartam, hogy a fordítások a technológia területén az angol a francia és\> a francia angol,\> azt létre két projekt: az egyik az angol - francia, és egy francia - angol. Azt adja meg az azonos kategóriában (technológia) mindkét és hagyja a projekt címke üres. A CategoryID mindkét projekt egyezne, így tudtam lekérdezésaz API angol és francia fordítások, anélkül, hogy módosítsa a CategoryID.

Ha Ön nyelvi szolgáltató, és több ügyfelet szeretne kiszolgálni különböző modellekkel, amelyek ugyanazt a kategóriát és nyelvet tartják meg, akkor a projektcímke használata az ügyfelek megkülönböztetésére bölcs döntés lenne.

## <a name="next-steps"></a>További lépések

- Olvassa el [a képzés és a modell](training-and-model.md) tudni, hogyan lehet hatékonyan építeni egy fordítási modellt.
