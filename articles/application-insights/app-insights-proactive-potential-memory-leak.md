---
title: "Észlelési - Azure Application Insights által észlelt potenciális memóriavesztés intelligens |} Microsoft Docs"
description: "Az Azure Application insights szolgáltatással lehetséges memóriavesztés az alkalmazások figyeléséhez."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: mbullwin
ms.openlocfilehash: 452d0a9d0231e54df2a7f1df76c3c2c0fcd94d87
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2017
---
# <a name="memory-leak-detection-preview"></a>Memória észlelését (előzetes verzió)

Az Application Insights automatikusan elemzi a memória-felhasználás az egyes folyamatok, az alkalmazásban, és képes figyelmeztessen a potenciális memóriavesztés vagy nagyobb memória-felhasználás.

Ehhez a szolgáltatáshoz nem speciális beállítási eltérő [teljesítményszámlálók konfigurálása](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-performance-counters) az alkalmazásra vonatkozóan. Aktív, ha elegendő memória teljesítményét számlálók telemetriai adatokat (például a saját memória) állít elő, az alkalmazás.


## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Ha visszajelzést kap az ilyen típusú intelligens észlelési értesítést?
Egy tipikus értesítés egy vagy több folyamatok és/vagy egy vagy több különböző, az alkalmazás részét képező idő (néhány órát), hosszú időn keresztül a rendszermemóriát konzisztens növekedése hajtsa végre.
Gépi tanulási algoritmusok memóriavesztés, természetesen az alkalmazás használatának megnövekedett megnövekedett rendszermemóriát ellentétben az egy bizonyos mintázatnak megfelelő nagyobb memória-felhasználás észlelésére szolgálnak.

## <a name="does-my-app-definitely-have-a-problem"></a>Saját alkalmazás mindenképpen rendelkezik probléma?
Nem, egy értesítés nem jelenti azt, hogy az alkalmazás véglegesen rendelkezik-e probléma. Bár memória memóriavesztés minták a alkalmazásproblémák általában azt jelzi, ezeket a mintákat lehet, hogy az adott folyamat tipikus vagy természetes üzleti indoklásának rendelkezhetnek, figyelmen kívül lesz hagyva.

## <a name="how-do-i-fix-it"></a>Hogyan tegye megjavítani?
Az értesítések tárgya lehet a diagnosztikai adatokat küld a diagnosztikai elemzési során támogatja:
1. **Osztályozás.** Az értesítés jeleníti meg, hogy memória mennyiségének növekedése (GB), és az időtartományt, amelyben a memória növekedett. Ez segíthet a probléma megoldásának prioritást.
2. **Hatókör.** Hány gépet tett a memória-memóriavesztés mintát? Hány kivételek váltotta a potenciális memóriavesztés során? Ez az információ az értesítés lehet lekérni.
3. **Diagnosztizálásához.** Az észlelési a memória adatszivárgás-mintát tartalmaz, megjelenítő memória-felhasználás a folyamat adott idő alatt. Használhatja a kapcsolódó elemeket, és jelentések csatolása támogató adatokat, további segítséget a probléma diagnosztizálásához.
