---
title: Az Azure Internet Analyzer hibaelhárítása | Microsoft dokumentumok
description: Az Azure Internet Analyzer hibaelhárítási hivatkozása.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: a265278652c16b4682707470d183a02a55b9a0ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77069217"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Az Azure Internet Analyzer hibaelhárítása

Ez a cikk az Internetes elemző általános problémáinak hibaelhárítási lépéseit tartalmazza.

## <a name="things-to-keep-in-mind"></a>Ügyeljen az alábbiakra
- Az ügyfélparancsfájlt **https-webhelybe** kell ágyazni. A mérések nem lesznek gyűjtve, ha a parancsfájl egyszerű szöveges (**http://**) vagy helyi (**file://**) webhelyen fut.
- A mérési adatok csak akkor lesznek gyűjtve, ha az Internet Analyzer profil ügyfélparancsfájlját beágyazták egy olyan alkalmazásba, amely valós felhasználói forgalmat kap. Szintetikus forgalom (például az Azure WebApp teljesítménytesztek) általában nem hajt végre beágyazott Javascript-kódot, így az adott típusú forgalom nem generál méréseket.

## <a name="azure-portal"></a>Azure portál
**"Scorecard még nem jött létre a kiválasztott szűrőkombinációhoz" a Scorecards szakaszban**
- Scorecards jönnek létre napi rendszerességgel (a végén minden nap, UTC idő).
- Scorecardok csak akkor jönnek létre, ha több mint 100 mérést gyűjtöttek össze a kiválasztott szűrőkombinációhoz (Teszt, Időszak, Ország stb.).

**Az "összes mérési szám" nulla a vizsgálat egy vagy mindkét végpontja esetében**
- Az idősorok és a mérési számok kiszámítása óránként egyszer lesz kiszámítva, így legalább ennyi időt kell várnia arra, hogy az új mérési adatok megjelenjenek.
- Az Internet Analyzer csak a sikeres méréseket számolja (azaz HTTP 200 válaszokat) az elemzéshez. Ha egy teszt egyik vagy mindkét végpontja nem érhető el, vagy nem 200-as HTTP-kódot ad vissza, akkor nulla összes méréssel jelennek meg.

## <a name="next-steps"></a>További lépések
Olvassa el az [Internet Analyzer GYIK-et](internet-analyzer-faq.md)
