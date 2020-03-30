---
title: Memóriavesztés észlelése – Az Azure Application Insights intelligens észlelése
description: Az Azure Application Insights segítségével figyelheti az alkalmazásokat a potenciális memóriavesztések miatt.
ms.topic: conceptual
ms.date: 12/12/2017
ms.openlocfilehash: 85d138518dfb1313a810657016e9fe3143887b6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671698"
---
# <a name="memory-leak-detection-preview"></a>Memóriavesztés észlelése (előzetes verzió)

Az Application Insights automatikusan elemzi az alkalmazás egyes folyamatainak memóriafelhasználását, és figyelmezteti a potenciális memóriavesztésre vagy a megnövekedett memóriafelhasználásra.

Ez a funkció nem igényel speciális beállítást, kivéve az alkalmazás [teljesítményszámlálóinak konfigurálását.](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) Akkor aktív, ha az alkalmazás elegendő memóriateljesítmény-számlálót (például privát bájtot) hoz létre.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Mikor kapom meg az ilyen típusú intelligens észlelési értesítést?
Egy tipikus értesítés a memóriahasználat hosszú időn keresztül iménynövekedését követi egy vagy több folyamatban és/vagy egy vagy több gépen, amelyek az alkalmazás részét képezik. A gépi tanulási algoritmusok a memóriavesztés mintájának megfelelő megnövekedett memóriafelhasználás észlelésére szolgálnak.

## <a name="does-my-app-really-have-a-problem"></a>Tényleg probléma van az alkalmazásommal?
Nem, az értesítés nem jelenti azt, hogy az alkalmazásnak feltétlenül problémája van. Bár a memóriavesztési minták általában egy alkalmazásproblémát jeleznek, ezek a minták az adott folyamatra jellemzőek lehetnek, vagy természetes üzleti indoklással rendelkezhetnek, és figyelmen kívül hagyhatók.

## <a name="how-do-i-fix-it"></a>Hogyan lehet kijavítani a hibát?
Az értesítések diagnosztikai információkat tartalmaznak a diagnosztikai elemzési folyamat támogatásához:
1. **Osztályozás.** Az értesítés megmutatja a memória növekedésének mértékét (GB-ban), és azt az időtartományt, amelyben a memória növekedett. Ez segíthet abban, hogy prioritást rendeljen a problémához.
2. **Hatókör.** Hány gépmutatta a memóriaszivárgást? Hány kivétel vált ki a potenciális memóriavesztés során? Ez az információ az értesítésből szerezhető be.
3. **Diagnosztizálni.** Az észlelés tartalmazza a memóriavesztés mintáját, amely a folyamat memóriafelhasználását mutatja az idő múlásával. A kapcsolódó elemeket és jelentéseket a támogató információkhoz csatolva is használhatja a probléma további diagnosztizálásához.
