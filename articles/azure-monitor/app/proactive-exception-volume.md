---
title: A kivételek mennyiségének rendellenes növekedése – Azure Application Insights
description: Alkalmazáskivételek figyelése az Azure Application Insights intelligens észlelési a kivételmennyiség szokatlan mintákat.
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: a08fae4774a8afb9959f55ea3196cd1a45c33439
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671766"
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>A kivétel mennyiségének rendellenes növekedése (előzetes verzió)

Az Application Insights automatikusan elemzi az alkalmazásban okozott kivételeket, és figyelmezteti a kivételtelemetria szokatlan mintáira.

Ez a funkció nem igényel speciális beállítást, kivéve az alkalmazás kivételjelentési beállításainak [konfigurálását.](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-exceptions#set-up-exception-reporting) Akkor aktív, ha az alkalmazás elegendő kivételtelemetriai adatokat hoz létre.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Mikor kapom meg az ilyen típusú intelligens észlelési értesítést?
Előfordulhat, hogy ilyen típusú értesítést kap, ha az alkalmazás egy adott típus kivételeinek száma rendellenesen növekszik egy nap alatt, az előző hét nap során számított alaptervhez képest.
Gépi tanulási algoritmusok at használnak a kivételek számának növekedésének észlelésére, miközben figyelembe veszi az alkalmazás használatának természetes növekedését.

## <a name="does-my-app-definitely-have-a-problem"></a>Biztos, hogy az alkalmazásomnak van problémája?
Nem, az értesítés nem jelenti azt, hogy az alkalmazásnak feltétlenül problémája van. Bár a kivételek túl nagy száma általában alkalmazásproblémát jelez, ezek a kivételek jóindulatúak lehetnek, és az alkalmazás megfelelően kezeli őket.

## <a name="how-do-i-fix-it"></a>Hogyan lehet kijavítani a hibát?
Az értesítések diagnosztikai információkat tartalmaznak a diagnosztikai folyamat támogatásához:
1. **Osztályozás.** Az értesítés megmutatja, hogy hány felhasználót vagy hány kérelmet érint. Ez segíthet abban, hogy prioritást rendeljen a problémához.
2. **Hatókör.** A probléma minden forgalmat érint, vagy csak valamilyen műveletet? Ez az információ az értesítésből szerezhető be.
3. **Diagnosztizálni.** Az észlelés információkat tartalmaz arról a módszerről, amelyből a kivétel történt, valamint a kivétel típusát. A kapcsolódó elemeket és jelentéseket a támogató információkhoz csatolva is használhatja a probléma további diagnosztizálásához.
