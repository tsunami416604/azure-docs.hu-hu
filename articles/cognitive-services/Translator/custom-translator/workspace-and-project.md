---
title: Mi egy munkaterületet, és a projektet? -Egyéni a fordítót
titleSuffix: Azure Cognitive Services
description: A munkaterület egy munkaterület létrehozása és az egyéni fordítási rendszerek kiépítésének. Munkaterület több projektek, a modellek és a dokumentumok tartalmazhatnak. Egy projektet egy modellt, a dokumentumok és a teszteket egy burkoló. Minden projekt automatikusan összes dokumentumot, amely a rendszer feltölti a munkaterülethez, amelyek rendelkeznek a megfelelő nyelvű pár tartalmaz.
services: cognitive-services
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: 6f88ff8ecd1aee588cb82c08ae2eda58fe2eb1e7
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51627440"
---
# <a name="what-is-a-custom-translator-workspace"></a>Mi az egyéni a fordítót munkaterület?

A munkaterület egy munkaterület létrehozása és az egyéni fordítási rendszerek kiépítésének. Munkaterület több projektek, a modellek és a dokumentumok tartalmazhatnak. Az egyéni Translator mégis munka egy adott munkaterületen belül van.

Munkaterület csak az Ön és a személyek, hogy meghívja a egyszerűen a munkaterületre. Uninvited személyek nem rendelkezik a munkaterületet a tartalmakhoz való hozzáférést. Tetszőleges számú személyek szerint, például egyszerűen a munkaterületre, és módosítja vagy hozzáférésüket bármikor küldhetnek meghívót. Egy új munkaterületet is létrehozhat. Alapértelmezés szerint a munkaterület nem tartalmaznak minden olyan projektek vagy a munkaterületeken, amelyek.

## <a name="what-is-a-custom-translator-project"></a>Mi az, hogy egy egyéni a fordítót projektet?

Egy projektet egy modellt, a dokumentumok és a teszteket egy burkoló. Minden projekt automatikusan összes dokumentumot, amely a rendszer feltölti a munkaterülethez, amelyek rendelkeznek a megfelelő nyelvű pár tartalmaz. Például ha egy angol, spanyol projekthez és a egy angol nyelvű projekthez spanyol, ugyanazokat a dokumentumokat fog szerepelni mindkét projekt. Minden projekthez van társítva CategoryID lekérdezésekor használt a [V3 API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) fordítások. CategoryID az egyéni a fordítót a használatával létrehozott testreszabott rendszer fordítások beszerzéséhez használt paraméter.

## <a name="project-categories"></a>Projekt kategóriák

A kategória a tartomány – a terminológia és stílus a használni kívánt terület – azonosítja a projekthez. Válassza ki a dokumentumok igényeinek legjobban megfelelő kategóriát. Bizonyos esetekben a kategória a többféle közvetlenül befolyásolja az egyéni a fordítót viselkedését.

Nem kell, hogy egyéni modellek kategóriák még egy általános alapkonfiguráció rendszer kivételével. De továbbra is javasoljuk, hogy felhasználóknak, hogy a legjobban megfelelő korlátozódjanak kategóriát, hogy a CategoryID azonosítóként használható. Projektek technológia a tartományban "Technológia" kiválasztása biztosítja, hogy amikor egy modell érhető el, hogy a projekt fogja tudni használni.

Ugyanazon a munkaterületen projektek ugyanarra a nyelv két különböző kategóriákban is létrehozhat. Egyéni a fordítót megakadályozza, hogy az azonos nyelv párokból és kategória az ismétlődő-projektek létrehozása. Egy címke alkalmazása a projekthez lehetővé teszi, hogy ez a korlátozás ne. Ne használja a címkéket, kivéve, ha több, mint a projekt egyedi címkék jelennek meg a projektek CategoryID hozzáadása az ügyfelek a fordítási rendszerek fejleszt.

## <a name="project-labels"></a>Projekt címkék

Egyéni a fordítót lehetővé teszi, hogy a projekt-címke hozzárendelése a projekthez. A projekt címke megkülönbözteti az azonos nyelv párokból és kategória több projektet. Ajánlott eljárásként, kerülje a projekt címkék, kivéve, ha szükséges.

A projekt címke a CategoryID részeként használatos. Ha a projekt címke adott Orientation marad, vagy a projektek között azonosan van beállítva, majd az ugyanazon kategória projektek és *különböző* nyelvi párok meg fogja osztani a azonos CategoryID. Ez a megközelítés akkor előnyös, mivel lehetővé teszi az Ön vagy az ügyfél a Translator Text API használatakor nem kell bajlódnunk a CategoryID minden projekt egyedi nyelvet szeretne váltani.

Például ha bármit szeretnék csinálni, engedélyezze a technológia tartomány angol, francia és francia az angol nyelvű fordításokat, I kell létrehoznia a két projekt: egyet az angol nyelvű tájékoztatáshoz -\> francia és a egy francia – a\> angol nyelven. Ehhez adja meg az azonos kategóriába (technológia) egyaránt és hagyja üresen a projekt címkét. Megfelel a CategoryID mindkét projekt esetében, így sikerült lekérdezni az API az angol és francia is fordítások az Outlook saját CategoryID módosítása nélkül.

Nyelvi szolgáltatást kínál, és több ügyfelet szolgál a különböző modell, amely megőrzi a kategóriába, és a nyelvi pár szeretne, ha a projekt címkék segítségével ügyfeleink megkülönböztetéséhez lenne ésszerű döntés.

## <a name="next-steps"></a>További lépések

- További információ [képzés és a modell](training-and-model.md) tudnia, hogyan lehet hatékonyan létrehozni egy fordítási modellben.