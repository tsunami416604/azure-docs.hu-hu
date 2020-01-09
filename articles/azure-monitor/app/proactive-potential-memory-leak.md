---
title: Memóriavesztés észlelése – Azure Application Insights intelligens észlelés
description: Alkalmazások figyelése az Azure Application Insights a lehetséges memória-szivárgáshoz.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 12/12/2017
ms.openlocfilehash: cc6074732a86be02a8d8c596c46e154db4e8ad7f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75406460"
---
# <a name="memory-leak-detection-preview"></a>Memóriavesztés észlelése (előzetes verzió)

A Application Insights automatikusan elemzi az alkalmazásban az egyes folyamatok memória-felhasználását, és figyelmezteti a lehetséges memória-szivárgásra vagy a megnövekedett memória-felhasználásra.

Ehhez a szolgáltatáshoz nem szükséges speciális beállítás, az alkalmazáshoz tartozó [teljesítményszámlálók](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) kivételével. Aktív, ha az alkalmazás elegendő memóriát generál a teljesítményszámlálók telemetria (például privát bájt).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Mikor kapok ilyen típusú intelligens észlelési értesítést?
Egy tipikus értesítés hosszú időn keresztül, egy vagy több folyamaton és/vagy egy vagy több gépen, az alkalmazás részét képező, állandó növekedést követ. A gépi tanulási algoritmusok a memória-szivárgás mintázatának megfelelő megnövekedett memóriahasználat észlelésére szolgálnak.

## <a name="does-my-app-really-have-a-problem"></a>Az alkalmazásom valóban probléma van?
Nem, egy értesítés nem azt jelenti, hogy az alkalmazásnak feltétlenül van problémája. Bár a memóriavesztés általában az alkalmazás hibáját jelzi, ezek a minták az adott folyamatra jellemzőek lehetnek, vagy természetes üzleti indoklással rendelkezhetnek, és figyelmen kívül hagyhatók.

## <a name="how-do-i-fix-it"></a>Hogyan lehet kijavítani a hibát?
Az értesítések a diagnosztikai elemzési folyamat támogatásához szükséges diagnosztikai információkat tartalmazzák:
1. **Osztályozás.** Az értesítés megjeleníti a memória növelésének mennyiségét (GB-ban), valamint azt az időtartományt, amelyben a memória növekedett. Ez segíthet a probléma prioritásának hozzárendelésében.
2. **Hatókör.** Hány gép mutatta a memóriavesztés mintáját? Hány kivételt váltott ki a potenciális memóriavesztés? Ezeket az információkat az értesítésből lehet beszerezni.
3. **Diagnosztizálása.** Az észlelés a memóriavesztés mintáját tartalmazza, amely a folyamat által az idő múlásával felhasznált memóriát mutatja. A probléma további diagnosztizálásához a kapcsolódó elemeket és jelentéseket is használhatja a támogatási információkhoz.
