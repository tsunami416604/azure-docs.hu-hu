---
title: "Észlelési - Azure Application Insights által észlelt felhőalapú erőforrások alacsony használatát intelligens |} Microsoft Docs"
description: "A felhőalapú erőforrások alacsony használatát az Azure Application insights szolgáltatással alkalmazások figyeléséhez."
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
ms.openlocfilehash: 8382f6047ae222a01cc0e8d6ca9dcf5593d0dff6
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2018
---
# <a name="low-utilization-of-cloud-resources-preview"></a>Alacsony kihasználtságú felhőalapú erőforrások (előzetes verzió)

Az Application Insights automatikusan elemzi a CPU-felhasználás az összes szerepkör-példány az alkalmazásban, és észleli a példányok biztosítanak alacsony Processzorigény mellett. Az észlelés lehetővé teszi, hogy csökkentse az Azure-erőforrások, és csökkentheti a költségeket, használja az egyes szerepkörökhöz szerepkörpéldányokat számának csökkentésével, vagy a szerepkörök számának csökkentésével.

Ehhez a szolgáltatáshoz nem speciális beállítási eltérő [teljesítményszámlálók konfigurálása](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) az alkalmazásra vonatkozóan. Az akkor aktív, ha az alkalmazás elegendő Processzor teljesítményének számláló telemetriai (% processzoridő) hoz létre.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Ha visszajelzést kap az ilyen típusú intelligens észlelési értesítést?
Egy tipikus értesítés következik be, amikor a webes/munkavégző szerepkörpéldányokat számos virágot alacsony Processzorigény mellett.

## <a name="does-my-app-definitely-consume-too-many-resources"></a>Nem saját alkalmazás mindenképpen erőforrást túl sok?
Nem, egy értesítés nem jelenti azt, hogy az alkalmazás túl sok erőforrás véglegesen használ fel. Ilyen mintákat keressen az alacsony processzorhasználat általában azt jelzi, hogy a hálózatierőforrás-fogyasztás csökkenteni sikertelen lehet, bár ez a viselkedés lehet, hogy általában az adott szerepkörhöz, vagy rendelkezhetnek a természetes üzleti indoklásának, figyelmen kívül lesz hagyva. Például lehet, hogy több példányt más erőforrások, például a memória és a hálózati és a Processzor nem szükséges.

## <a name="how-do-i-fix-it"></a>Hogyan tegye megjavítani?
Az értesítések tárgya lehet a diagnosztikai adatokat küld a diagnosztika folyamat támogatja:
1. **Osztályozás.** Az értesítés a szerepkörök megjeleníti az alkalmazás, amelyeket az alacsony Processzorigény mellett. Ez segíthet a probléma megoldásának prioritást.
2. **Hatókör.** Hány szerepkör tett alacsony Processzorigény mellett, és minden egyes szerepkör-hány példánya alacsony CPU felhasználását? Ez az információ az értesítés lehet lekérni.
3. **Diagnosztizálásához.** Az észlelési CPU funkcióját, CPU-felhasználását minden példány időbeli megjelenítő tartalmazza. A kapcsolódó elemek és a támogatási információkat, például a százalékos érték a CPU-felhasználás csatolása jelentések segítséget nyújtanak a probléma további diagnosztizálása is használja.
