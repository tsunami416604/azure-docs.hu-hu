---
title: Mi az a munkaterület és a projekt? -Egyéni a fordítót
titleSuffix: Azure Cognitive Services
description: A munkaterület az egyéni fordítási rendszer összeállításához és létrehozásához használható munkaterület. A munkaterületek több projektet, modellt és dokumentumot is tartalmazhatnak. A projekt egy modell, dokumentum és teszt burkolója. Minden projekt automatikusan tartalmazza az adott munkaterületre feltöltött összes dokumentumot, amely a megfelelő nyelvi párral rendelkezik.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 23db27ba7b51b0f5a312498dbcb1d0f2741245bb
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595343"
---
# <a name="what-is-a-custom-translator-workspace"></a>Mi az egyéni Translator-munkaterület?

A munkaterület az egyéni fordítási rendszer összeállításához és létrehozásához használható munkaterület. A munkaterületek több projektet, modellt és dokumentumot is tartalmazhatnak. Az egyéni fordítóban végzett összes művelet egy adott munkaterületen belül van.

A munkaterület saját maga és a munkaterületen meghívott személyek számára. A nem meghívott személyek nem férhetnek hozzá a munkaterület tartalmához. Tetszőleges számú személyt meghívhat a munkaterületre, és bármikor módosíthatja vagy eltávolíthatja a hozzáférését. Új munkaterületet is létrehozhat. Alapértelmezés szerint a munkaterület nem tartalmaz olyan projektet vagy dokumentumot, amely a többi munkaterületen belül található.

## <a name="what-is-a-custom-translator-project"></a>Mi az egyéni fordítói projekt?

A projekt egy modell, dokumentum és teszt burkolója. Minden projekt automatikusan tartalmazza az adott munkaterületre feltöltött összes dokumentumot, amely a megfelelő nyelvi párral rendelkezik. Ha például egy angol – spanyol és egy spanyol – angol projekt is van, akkor mindkét projektben ugyanazokat a dokumentumokat fogja tartalmazni. Minden projekthez hozzá van rendelve egy Kategóriakód, amely a [V3 API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) fordításokhoz való lekérdezéséhez használatos. A Kategóriakód a Custom Translatortel létrehozott, testreszabott rendszerből származó fordítások beolvasására szolgáló paraméter.

## <a name="project-categories"></a>Projektek kategóriái

A kategória azonosítja a tartományt – a használni kívánt terminológia és stílus területét – a projekthez. Válassza ki a dokumentumokhoz leginkább illő kategóriát. Bizonyos esetekben a kategória választása közvetlenül befolyásolja az egyéni fordító viselkedését.

Két alapmodellből álló készletünk van. Általános és technológiai. Ha a kategória **technológiája** be van jelölve, a rendszer a technológiai alapmodelleket fogja használni. Bármely más kategória kiválasztásakor a rendszer az általános alapmodelleket használja. A technológiai alapmodell jól működik a technológiai tartományban, de alacsonyabb minőséget mutat, ha a fordításhoz használt mondatok nem tartoznak a technológiai tartományba. Javasoljuk, hogy az ügyfelek csak akkor válasszanak a kategória technológiát, ha a mondatok szigorúan a technológiai tartományon belül esnek.

Ugyanebben a munkaterületen különböző kategóriákban hozhat létre projekteket ugyanahhoz a nyelvi párosításhoz. Az egyéni fordító meggátolja egy ismétlődő projekt létrehozását ugyanazzal a nyelvi párral és kategóriával. Ha a címkét a projektre alkalmazza, ezzel elkerülhető a korlátozás. Ne használjon feliratokat, kivéve, ha több ügyfél számára készít fordítási rendszereket, mivel a projekthez tartozó egyedi címke hozzáadása a projektek Kategóriakód-ban jelenik meg.

## <a name="project-labels"></a>Projekt feliratai

Az egyéni fordító lehetővé teszi a projekthez tartozó címke hozzárendelését. A projekt felirata megkülönbözteti a több projekt között ugyanazzal a nyelvi párral és kategóriával. Ajánlott eljárásként lehetőleg kerülje a címkék használatát, ha szükséges.

A projekt címkéje a Kategóriakód részeként van használatban. Ha a projekt felirata balra van állítva, vagy azonos módon van beállítva a projektek között, akkor az azonos kategóriájú és *különböző* nyelvi párokkal rendelkező projektek ugyanazt a Kategóriakód-t fogják megosztani. Ez a megközelítés azért előnyös, mert lehetővé teszi, hogy az Ön vagy az ügyfele átváltson a nyelvek között a szöveges Translator API használata nélkül anélkül, hogy az egyes projektekre jellemző Kategóriakód-t kellene aggódnia.

Ha például azt szeretném, hogy a technológiai tartomány fordításait angolról franciára vagy franciáról angolra szeretném engedélyezni, hozzon létre két projektet: egy az\> angol – francia nyelven, egy pedig\> francia – angol nyelven. Ugyanazt a kategóriát (technológiát) szeretném megadni, és a projekt címkéjét üresen hagyják. A két projekthez tartozó Kategóriakód is egyezik, ezért a Kategóriakód módosítása nélkül is lekérhetem az API-t angol és francia nyelvű fordításra.

Ha Ön nyelvi szolgáltató, és több olyan ügyfelet szeretne kiszolgálni, akik különböző modellekkel rendelkeznek, és megtartják ugyanazt a kategóriát és nyelvi párt, akkor a projekt címkéje az ügyfelek közötti különbségtételhez bölcs döntés lenne.

## <a name="next-steps"></a>További lépések

- Tájékozódjon a betanításról és a modellről, [és](training-and-model.md) Ismerje meg, hogyan hozhat létre hatékony fordítási modellt.
