---
title: Intelligens detektálás – Azure Application Insights által észlelt a felhőbeli erőforrások alacsony használatát |} A Microsoft Docs
description: A felhőbeli erőforrások alacsony használatát az Azure Application Insights alkalmazások figyelésére.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: mbullwin
ms.openlocfilehash: ca4f944f605db96a2cedf2682f3ff4c811007ffb
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35644904"
---
# <a name="low-cpu-utilization-in-cloud-resources-preview"></a>Mérsékelt processzorigény a felhőbeli erőforrások (előzetes verzió)

Application Insights automatikusan elemzi a CPU-felhasználását, minden egyes szerepkör-példány az alkalmazásban, és észleli a példányok alacsony processzorigény mellett. Az észlelés lehetővé teszi, hogy csökkentse az Azure-erőforrások, és csökkentheti a költségeket, minden egyes szerepkör már használja a szerepkörpéldányok számának csökkentésével, vagy a szerepkörök számának csökkentésével.

Ez a funkció nem speciális beállítás nem szükséges [konfigurálása teljesítményszámlálók](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) az alkalmazáshoz. Ez akkor aktív, ha az alkalmazása elég Processzor teljesítményének számláló telemetriát (%-ban a processzoron) hoz létre.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Ha szeretné be intelligens detektálás értesítést az ilyen típusú?
Egy tipikus értesítési számos, a webes/feldolgozói szerepkörpéldányok mutatnak, alacsony processzorigény következik be.

## <a name="does-my-app-definitely-consume-too-many-resources"></a>Nem az alkalmazás mindenképp felhasználása túl sok erőforrást?
Nem, egy értesítés nem jelenti azt, hogy az alkalmazás túl sok erőforrást mindenképp használ fel. Bár az ilyen minták alacsony CPU-felhasználás mintavételezését általában azt jelzik, hogy az erőforrás-használat sikerült csökkenthető, ez a viselkedés általában az az adott szerepkörrel lehet vagy természetes üzleti indoklásának rendelkezhet, és figyelmen kívül hagyható. Például annak oka az lehet, hogy több példányt más erőforrások, például a memória és a hálózati és a Processzor nem szükséges.

## <a name="how-do-i-fix-it"></a>Hogyan javíthatom?
Az értesítések közé tartoznak a diagnosztikai adatokat a diagnosztika során támogatja:
1. **Osztályozási.** Az értesítés bemutatja, a szerepkörök az alkalmazásban, amelyeket CPU-kihasználtság alacsony. Ez segíthet a probléma prioritást rendelhet.
2. **A hatókör.** Hány szerepkör által alacsony processzorigény, és hány példányt minden szerepkörben alacsony CPU használni? Ez az információ szerezhető az értesítést.
3. **Diagnosztizálja a.** Az észlelés a CPU használt fel, megjelenítése az idő függvényében az egyes példányok CPU-kihasználtság százalékos tartalmazza. Használhatja a kapcsolódó elemek és támogatási információk, például a CPU-felhasználás mintavételezését. percentilisei összekapcsolása jelentések segítséget nyújtanak a probléma további diagnosztizálása.
