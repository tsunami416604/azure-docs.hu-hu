---
title: Klasszikus riasztások áttekintése a Microsoft Azure-ban és az Azure Monitorban
description: A klasszikus riasztások elavultak. Riasztások lehetővé teszik, hogy az Azure-erőforrás-metrikák, események vagy naplók figyelése, és értesítést kap, ha egy megadott feltétel teljesül.
ms.topic: conceptual
ms.date: 05/19/2018
ms.subservice: alerts
ms.openlocfilehash: 098efd3075c6b099bdfc925cb4f09163f83532a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668266"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Mik a Microsoft Azure klasszikus riasztásai?

> [!NOTE]
> Ez a cikk ismerteti, hogyan hozhat létre régebbi klasszikus metrika riasztások. Az Azure Monitor mostantól támogatja az [újabb, közel valós idejű metrikariasztásokat és az új riasztások élményét.](../../azure-monitor/platform/alerts-overview.md) Klasszikus riasztások [kivonva,](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement)bár továbbra is korlátozottan használják az erőforrásokat, amelyek még nem támogatják az új riasztásokat. 
>

Riasztások lehetővé teszik, hogy konfigurálja a feltételeket az adatok felett, és értesítést kap, ha a feltételek megfelelnek a legújabb figyelési adatokat.

## <a name="old-and-new-alerting-capabilities"></a>Régi és új riasztási lehetőségek

A korábbi Azure Monitor, Application Insights, Log Analytics és service Health külön riasztási képességekkel rendelkezett. Túlóra, az Azure továbbfejlesztett és kombinált mind a felhasználói felület és a különböző riasztási módszerek. A konszolidáció még folyamatban van.

A klasszikus riasztások csak a klasszikus riasztások felhasználói képernyőn az Azure Portalon. Ezt a képernyőt a **Klasszikus riasztások megtekintése** gomba a riasztások képernyőn kapja meg. 

 ![Riasztási lehetőségek az Azure Portalon](media/alerts-classic.overview/monitor-alert-screen2.png)

Az új riasztások felhasználói élménye a következő előnyökkel jár a klasszikus riasztások felületén:
-   **Jobb értesítési rendszer** – Minden újabb riasztás műveletcsoportokat használ, amelyek nevesített értesítési csoportok és műveletek, amelyek több riasztásban is újra felhasználhatók. Klasszikus metrikariasztások és a régebbi Log Analytics-riasztások nem használnak műveletcsoportokat.
-   **Egységes szerzői élmény** – Az Azure Monitoron, a Log Analytics szolgáltatásban és az Application Insightsban található összes riasztás létrehozása metrikákhoz, naplókhoz és tevékenységnaplóhoz egy helyen van.
-   **A kirúgott Log Analytics-riasztások megtekintése az Azure Portalon** – most már az előfizetésében is láthatja a kirúgott Log Analytics-riasztásokat. Korábban ezek egy külön portálon voltak.
-   **A tüzelt riasztások és riasztási szabályok elkülönítése** – Riasztási szabályok (a riasztást kiváltó állapot definíciója) és a tüzelt riasztások (a riasztási szabály tüzelési példánya) differenciálódnak, így a működési és konfigurációs nézetek elkülönülnek.
-   **Jobb munkafolyamat** – Az új riasztások szerzői élménye végigvezeti a felhasználót a riasztási szabály konfigurálásának folyamatában, ami egyszerűbbé teszi a megfelelő dolgok felderítését a figyelmeztetéshez.
-   **Intelligens riasztások összevonása** és **riasztási állapot beállítása** – Az újabb riasztások közé tartozik az automatikus csoportosítási funkció, amely hasonló riasztásokat jelenít meg a felhasználói felület túlterhelésének csökkentése érdekében. 

Az újabb metrikariasztások a következő előnyökkel járnak a klasszikus metrikariasztásokhoz:
-   **Továbbfejlesztett késés:** Az újabb metrikariasztások egy percenként olyan gyakran futhatnak, mint minden percben. A régebbi metrikariasztások mindig 5 perces gyakorisággal futnak. Az újabb riasztások száma a probléma előfordulásától az értesítésig vagy a műveletig (3–5 perc) egyre kisebb. A régebbi riasztások a típustól függően 5 és 15 perc között vannak.  A naplóriasztások általában 10–15 perces késleltetéssel rendelkeznek a naplók betöltéséhez szükséges idő miatt, de az újabb feldolgozási módszerek csökkentik ezt az időt. 
-   **Többdimenziós metrikák támogatása:** Riasztást adhat a dimenziós metrikákról, amelyek lehetővé teszik a metrika érdekes szegmensének figyelését.
-   **A metrikafeltételek további szabályozása:** Gazdagabb riasztási szabályokat határozhat meg. Az újabb riasztások támogatják a metrikák maximális, minimális, átlagos és összes értékének figyelését.
-   **Több metrika kombinált figyelése:** Több metrikát (jelenleg legfeljebb két metrikát) figyelhet egyetlen szabállyal. Riasztás akkor aktiválódik, ha mindkét metrika megsérti a megadott időszak vonatkozó küszöbértékeket.
-   **Jobb értesítési rendszer**: Minden újabb riasztás [műveletcsoportokat](../../azure-monitor/platform/action-groups.md)használ, amelyek nevesített értesítési csoportok és műveletek, amelyek több riasztásban is felhasználhatók.  Klasszikus metrikariasztások és a régebbi Log Analytics-riasztások nem használnak műveletcsoportokat. 
-   **Metrikák a naplók (nyilvános** előzetes verzió): A Log Analytics bekerülő adatok naplózása mostmár kinyerhető és átalakítható az Azure Monitor metrikák, majd riasztást, mint más metrikák. Lásd: [Riasztások (klasszikus)](alerts-classic.overview.md) a klasszikus riasztásokra jellemző terminológiát. 


## <a name="classic-alerts-on-azure-monitor-data"></a>Klasszikus riasztások az Azure Monitor adatairól
A klasszikus riasztások nak két típusa van – metrikariasztások és tevékenységnapló-riasztások.

* **Klasszikus metrikariasztások** – Ez a riasztás akkor aktiválódik, ha egy megadott metrika értéke átlépi a hozzárendelt küszöbértéket. A riasztás értesítést hoz létre, ha a küszöbértéket átlépi, és a riasztási feltétel teljesül. Ezen a ponton a riasztás "Aktiváltnak" minősül. Ez létrehoz egy másik értesítést, ha a "Megoldott" - azaz, ha a küszöbértéket ismét átlépik, és a feltétel már nem teljesül.

* **Klasszikus tevékenységnapló-riasztások** – egy streamelési napló riasztás, amely a szűrőfeltételeknek megfelelő tevékenységnapló-eseménybejegyzésen aktiválódik. Ezek a riasztások csak egy állam, "Aktivált". A riasztási motor egyszerűen alkalmazza a szűrő feltételeket minden új esemény. Nem keres régebbi bejegyzéseket. Ezek a riasztások értesítést küldhetnek, ha új Service Health-incidens történik, vagy ha egy felhasználó vagy alkalmazás műveletet hajt végre az előfizetésben, például "Virtuális gép törlése".

Az Azure Monitoron keresztül elérhető erőforrásnapló-adatok esetén továbbítsa az adatokat a Log Analytics szolgáltatásba, és használjon naplólekérdezési riasztást. A Log Analytics mostantól az [új riasztási módszert](../../azure-monitor/platform/alerts-overview.md) használja 

Az alábbi ábra összefoglalja az Azure Monitor adatforrásait, és fogalmilag, hogyan riasztást az adatokról.

![Magyarázata](media/alerts-classic.overview/Alerts_Overview_Resource_v5.png)

## <a name="taxonomy-of-alerts-classic"></a>A riasztások taxonómia (klasszikus)
Az Azure a következő kifejezéseket használja a klasszikus riasztások és azok funkcióinak leírására:
* **Riasztás** – a feltételek (egy vagy több szabály vagy feltétel) meghatározása, amely akkor aktiválódik, ha teljesül.
* **Aktív** – az az állapot, amikor a klasszikus riasztás által meghatározott feltételek teljesülnek.
* **Megoldott** – az az állapot, amikor a klasszikus riasztás által meghatározott feltételek már nem teljesülnek a korábban teljesült után.
* **Értesítés** – a klasszikus riasztás aktívvá válása alapján végrehajtott művelet.
* **Művelet** – egy adott hívás, amelyet egy értesítés fogadójának küldenek (például cím küldése vagy egy webhook URL-címre való közzététel). Az értesítések általában több műveletet is elindíthatnak.

## <a name="how-do-i-receive-a-notification-from-an-azure-monitor-classic-alert"></a>Hogyan kaphatok értesítést egy klasszikus Azure Monitor-riasztástól?
A különböző szolgáltatásokból származó Azure-riasztások korábban saját beépített értesítési módszereket használtak. 

Az Azure Monitor létrehozott egy *műveletcsoportoknak*nevezett újrafelhasználható értesítési csoportot. A műveletcsoportok egy értesítés fogadókészletét adják meg. Minden alkalommal, amikor egy riasztás aktiválódik, amely hivatkozik a műveletcsoport, minden fogadók megkapja az értesítést. A műveletcsoportok lehetővé teszik a fogadók (például az ügyeleti mérnöklista) csoportosításának újrafelhasználását számos riasztási objektum között. A műveletcsoportok támogatják az értesítést azáltal, hogy az e-mail címeken, SMS-számokon és számos egyéb műveleten kívül egy webhook URL-címre is közzéteszik az értesítést.  További információt a műveletcsoportok című [témakörben talál.](../../azure-monitor/platform/action-groups.md) 

A régebbi klasszikus tevékenységnapló-riasztások műveletcsoportokat használnak.

A régebbi metrikariasztások azonban nem használnak műveletcsoportokat. Ehelyett a következő műveleteket konfigurálhatja: 
- E-mail értesítésekküldése a szolgáltatás rendszergazdájának, a társrendszergazdáknak vagy a megadott további e-mail címeknek.
- Hívjon meg egy webhookot, amely lehetővé teszi további automatizálási műveletek indítását.

A webhookok lehetővé teszik az automatizálást és a szervizelést, például a következők használatával:
- Azure Automation-runbook
- Azure-függvény
- Azure Logic alkalmazás
- harmadik fél től származó szolgáltatás

## <a name="next-steps"></a>További lépések
A riasztási szabályokról az:

* További információ [a mérőszámokról](data-platform.md)
* [Klasszikus metrikariasztások konfigurálása az Azure Portalon keresztül](alerts-classic-portal.md)
* [Klasszikus metrikariasztások konfigurálása PowerShell](alerts-classic-portal.md)
* [Klasszikus metrikus riasztások parancssori felületének konfigurálása (CLI)](alerts-classic-portal.md)
* [Klasszikus metrikariasztások konfigurálása Az Azure Monitor REST API-ja](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* További információ [a tevékenységnaplóról](platform-logs-overview.md)
* [Tevékenységnapló-riasztások konfigurálása az Azure Portalon keresztül](activity-log-alerts.md)
* [Tevékenységnapló-riasztások konfigurálása az Erőforrás-kezelőn keresztül](alerts-activity-log.md)
* Tekintse át a [tevékenységnapló riasztási webhook-sémáját](activity-log-alerts-webhook.md)
* További információ a [műveletcsoportokról](action-groups.md)
* [Újabb riasztások konfigurálása](alerts-metric.md)
