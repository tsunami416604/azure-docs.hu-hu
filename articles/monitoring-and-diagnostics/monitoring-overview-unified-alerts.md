---
title: Az új riasztások felületet Azure figyelőben felfedezése |} Microsoft Docs
description: Hogyan szerzői, megtekintése és egyszerűbb riasztások kezelése az Azure elérhetővé válnak az új egyszerű és méretezhető riasztások tapasztalatai ismertetése
author: manishsm-msft
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: mamit
ms.custom: ''
ms.openlocfilehash: c3622b4699ef532f204231c76aa3436be3676763
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="the-new-alerts-experience-in-azure-monitor"></a>Az Azure-figyelő tapasztal az új riasztások

## <a name="overview"></a>Áttekintés

> [!NOTE]
> Ez a cikk ismerteti az újabb riasztásokat. Ismerteti a klasszikus Azure-figyelő riasztásokat régebbi [klasszikus riasztások áttekintése](monitoring-overview-alerts.md). 
>
>

Riasztások rendelkezik az új felület. A régebbi riasztások élmény pedig mostantól a riasztások (klasszikus) lapján. Az új riasztások felületet a riasztások (klasszikus) élmény van a következő előnyöket biztosítja:

-   **Értesítési rendszer jobb**: minden újabb riasztás használja [művelet csoportok]( https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups), amely megnevezett értesítések és a műveletek a több riasztás is használhatnak.  Klasszikus metrika riasztások és értesítések. Naplóelemzési régebbi. ne használjon művelet csoportok. 
- **A jelentéskészítési egyesített** - összes riasztás létrehozása a metrikákat, naplók és a tevékenység napló keresztül figyelő Azure Log Analytics és az Application Insights rendelkezésre áll egy. 
- **Nézet indította Naplóelemzési riasztásait az Azure portálon** -most is lásd indította Naplóelemzési riasztások az előfizetésében is. Korábban ezek volt külön portált. 
- **Fired riasztások és a riasztás szabályok** - riasztás szabályok (a riasztást kiváltó definíció), és következik be riasztásokat (a riasztási szabály indítási példánya) megkülönböztetett forgalomosztályból, így elkülönül egymástól a működési és konfigurációs nézetek.
- **Jobb munkafolyamat** – az új riasztások szerzői élmény útmutatók a felhasználó mentén a riasztási szabályt, amely megfelelő a következőket a küldjön riasztást Önnek felderítéséhez egyszerűbbé teszi konfigurálásának lépésein.
 
Újabb metrika riasztások kifejezetten rendelkezik a következő fejlesztéseket kínálja:
-   **Továbbfejlesztett késés**: újabb metrika riasztások egy percenként gyakorisággal futtatható. Régebbi metrika riasztások, 5 perces gyakorisággal végezni. Napló riasztásokat továbbra is fennáll a hosszabb, mint 1 perces késleltetési idő miatt tölti be a naplók vesz igénybe. 
-   **A többdimenziós metrikák támogatása**: figyelmeztetik a dimenzionális mérőszámokat, lehetővé téve a metrika az érdekes szegmens figyelése.
-   **Metrika feltételek teljesebb körű vezérlése**: gazdagabb riasztási szabályokat adhat meg. Az újabb riasztások a maximális, minimális, átlagos, és az összes érték a mérőszámok figyelésére alkalmas.
-   **Egyszerre több metrikák figyelését**: figyelheti a (jelenleg legfeljebb két metrikák) kezelhető egyetlen szabállyal több metrikákat. Riasztást vált ki, ha mindkét metrikák megsértik a megfelelő küszöbértéket a megadott időszakban.
-   **A naplók metrikák** (korlátozott nyilvános előzetes verzió): bizonyos adatokat, amelyek Log Analytics mostantól napló kicsomagolja és Azure figyelő metrikák alakítja át és más metrikákkal hasonlóan a majd riasztást. 



A következő szakaszok ismertetik, részletesebben, az új felület működése.

## <a name="alert-rules-terminology"></a>A riasztási szabályok terminológia
Az új riasztások felületet használja a következő fogalmak a riasztást szabály és a riasztás indította objektumok szétválasztásának közben egységes a szerzői műveletekhez élmény különböző riasztástípusok között.

- **Célerőforrás** -cél lehet az Azure-erőforrásokkal. Cél erőforráson hatókör és jelek érhető el, mert így határozza meg. Példa célok: a virtuális gép, egy tárfiókot, a virtuálisgép-méretezési csoport, a Naplóelemzési munkaterület vagy az Application Insights-erőforrás.

- **Feltételek** – feltételek jel kombinációja és logika alkalmazza a cél erőforráson. Példák: Százalékos CPU > 70 % kiszolgáló válaszideje > 4 ms, a napló eredményszám lekérdezése a > 100 stb. 

- **Jel** - jelzi a célerőforrás által kibocsátott, és több típusúak lehetnek. **A metrika**, **tevékenységnapló**, **Application Insights**, és **napló** támogatott jel típusa.

- **Logikai** -felhasználó által meghatározott logikát, ellenőrizze, hogy a jel belül-e a várt tartományon és-értékek.  
 
- **A művelet** – olyan adott műveletet hajt végre, amikor a riasztás lép működésbe. Például az e-mail címet e, vagy hívja a webhook URL-CÍMÉT. Több művelet akkor fordulhat elő, ha egy riasztás akkor következik be. Ezek a riasztások művelet csoportokat támogatja.  
 
- **Riasztási szabály** -a feltétellel, hogy szeretné aktiválni a riasztást. A riasztási szabályt, mert így a cél és a feltételek rögzíti. A riasztási szabály egy engedélyezett vagy letiltott állapotban lehet.
 
    > [!NOTE]
    > Ez eltér a riasztások (klasszikus) élmény, ahol a riasztás a szabály és a égetett riasztás és ezért figyelmeztetés, aktív vagy le van tiltva állapota lehet.
    >

## <a name="single-place-to-view-and-manage-alerts"></a>Egy helyen riasztások megtekintése és kezelése
A riasztások élmény célja a egyetlen hely, az összes Azure-riasztások megtekintése és kezelése. Az alábbi alszakaszok az új felület minden egyes képernyőjén feladatait ismertetik.

### <a name="alerts-overview-page"></a>Riasztások – áttekintés oldalra
**Monitor - riasztás** – áttekintés oldalra a égetett riasztások összesített összegzését jeleníti meg, és teljes konfigurált/engedélyezve van a riasztási szabályok. Emellett az összes égetett riasztás listáját jeleníti meg. Az előfizetések, illetve a szűrő paramétereit frissíti az összesítések, és a riasztások listája következik be.

> [!NOTE]
> Riasztások látható égetett riasztások korlátozódnak támogatott metrika és napló tevékenységriasztásokat; Az Azure figyelő áttekintése látható égetett, beleértve a korábbi Azure riasztásokat a riasztások száma

 ![riasztások – áttekintés](./media/monitoring-overview-unified-alerts/alerts-preview-overview2.png) 

### <a name="alert-rules-management"></a>A riasztási szabályok kezelése
**Monitor - riasztás > szabályok** egy egyetlen lap minden riasztási szabályok kezelése az Azure-előfizetések között. Felsorolja az összes riasztási szabályok (engedélyezett vagy tiltott) és a tároló erőforrásait, az erőforráscsoportok, a szabály neve vagy a állapota alapján rendezhető. A riasztási szabályok is le van tiltva vagy engedélyezhető vagy szerkeszteni ezen a lapon.  

 ![riasztások-szabályok](./media/monitoring-overview-unified-alerts/alerts-preview-rules.png)


## <a name="one-alert-authoring-experience-across-all-monitoring-sources"></a>Jelentéskészítő környezetét minden figyelési forrás egy riasztás
Az új riasztások élményt nyújt a riasztásokat függetlenül a figyelőszolgáltatás egységes módon hozhat létre, vagy a típus azt. Az összes riasztás következik be, és a kapcsolódó részletes adatok találhatók egylapos.  
 
Riasztás szerzői egy három lépéses feladat ahol a felhasználó először választja ki egy cél a riasztást, a jobb oldali jel kiválasztásával, és majd megadja az alkalmazandó a jel a riasztási szabály részeként logika követ. Az egyszerűsített létrehozási folyamat már nem szükséges tudni, hogy a figyelési forrás- vagy egy Azure-erőforrás kiválasztása előtt támogatott jelek a felhasználó. A közös szerzői élmény automatikusan kijelölt tároló-erőforrás alapján elérhető jelek listájának szűrése, és végig is vezeti riasztási logika létrehozása

További létrehozásával, a következő riasztástípusok [Itt](monitor-alerts-unified-usage.md).
- Metrikákhoz kapcsolódó riasztások
- Napló riasztások (Naplóelemzési)
- Napló riasztások (tevékenységi naplóit)
- Napló riasztások (Application Insights)

 
## <a name="alerts-supported-in-new-experience"></a>Az új felület támogatott riasztások
Riasztások több Azure-szolgáltatások figyelésének keresztül érhetők el. Információk hogyan és mikor érdemes használni ezeket a szolgáltatásokat [ebben a cikkben találhat](./monitoring-overview.md). Itt érhető el részletes információkat a riasztástípusok között Azure, és mi jelenleg támogatja az új riasztások felületet. 


| **Jel típusa** | **Forrás figyelése** | **Leírás** | 
|-------------|----------------|-------------|
| Metrika | Az Azure-figyelő | Más néven [közel valós idejű metrika riasztások](monitoring-near-real-time-metric-alerts.md), 1 perces gyakorisággal metrika feltételek kiértékelése támogatja, és lehetővé teszik a több metrika és többdimenziós metrika szabályokat. A támogatott erőforrástípusai listáját [Itt](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported). |
| Metrika | Az Azure-figyelő | [Régebbi klasszikus metrika riasztások](monitoring-overview-alerts.md) nem támogatottak az új riasztások felületet. Megtalálja azokat a riasztásokat (klasszikus) Azure-portálon. A klasszikus riasztások néhány metrikák típusokat, amelyek még nem lettek áthelyezve az újabb riasztások támogatja. Teljes listájáért lásd: [támogatott metrikák](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-supported-metrics)
| Logs  | Log Analytics | Értesítéseket, illetve automatikus műveleteket futtatott metrika és/vagy esemény adatok napló keresési lekérdezés meghatározott feltételeknek eleget. Régebbi Naplóelemzési riasztásokat továbbra is elérhetők, de [az új felületre való másolásának](monitoring-alerts-extend.md). Emellett egy [az előzetes *Naplóelemzési naplók, metrikák* ](monitoring-alerts-extend-tool.md) érhető el. Az előzetes lehetővé teszi bizonyos naplók típusait és alakíthatja át őket metrika, ahol meg tudja majd riasztás őket az Új riasztási felület használatával. Az előzetes akkor hasznos, ha le szeretné kérdezni natív Azure figyelő metrikák együtt az Azure-naplók. |
| Tevékenységnapló | Tevékenységi naplóit (általános) | A kiválasztott célkiszolgáló (erőforrás és az erőforrások csoport/előfizetést) keresztül végrehajtott összes létrehozási, frissítési és törlési műveletek rekordokat tartalmazza. |
| Tevékenységnapló  | Service Health | Az új riasztások tapasztalat nem támogatott. Lásd: [napló riasztások tevékenység létrehozása a szolgáltatás értesítések](monitoring-activity-log-alerts-on-service-notifications.md).  |
| Logs  | Application Insights | Naplók és az alkalmazás teljesítményének részleteit tartalmazza. Analytics query használatával végrehajtott – az alkalmazásadatok alapján műveletek feltételeinek megadása. |
| Metrika | Application Insights | Az új riasztások tapasztalat nem támogatott. Lásd: [metrika riasztások](../application-insights/app-insights-alerts.md) |
| Webalkalmazás-rendelkezésreállás figyelésére szolgáló tesztek | Application Insights | Nem támogatott a riasztások élményt nyújt.  Lásd: [webes teszt riasztások](../application-insights/app-insights-monitor-web-app-availability.md). Bármely szeretnék adatokat küldeni a Application Insights tagolva webhelyen érhető el. Értesítést kaphat, ha a rendelkezésre állás vagy a webhely válaszképesség nem éri el elvárásainak. |




## <a name="next-steps"></a>További lépések
- [Az új riasztások felület használata létrehozása, megtekintése és riasztások kezelése](monitor-alerts-unified-usage.md)
- [További tudnivalók a napló riasztásokat a riasztások élmény](monitor-alerts-unified-log.md)
- [További tudnivalók a metrika riasztásokat a riasztások élmény](monitoring-near-real-time-metric-alerts.md)
- [További tudnivalók a tevékenység napló riasztásokat a riasztások élmény](monitoring-activity-log-alerts-new-experience.md)
