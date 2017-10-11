---
title: "Mik azok a szolgáltatás állapotával kapcsolatos értesítésekre |} Microsoft Docs"
description: "Szolgáltatás állapotával kapcsolatos értesítésekre engedélyezi, hogy a szolgáltatás állapotának üzenetek közzététele a Microsoft Azure által megtekintéséhez."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
ms.openlocfilehash: d85281c02b792921f12cc62e6d60bef3e7c13b3f
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="service-health-notifications"></a>Szolgáltatás állapotával kapcsolatos értesítésekre
## <a name="overview"></a>Áttekintés

Ez a cikk bemutatja, hogyan megtekintéséhez az Azure portál használatával a szolgáltatás állapotával kapcsolatos értesítésekre.

Szolgáltatás állapotával kapcsolatos értesítésekre engedélyezi, hogy a szolgáltatás állapotának üzenet, ami hatással lehet az erőforrásokat az előfizetéshez tartozó Azure-csapat által közzétett megtekintése. Ezek az értesítések tevékenység alárendelt osztályát alkalmazásnapló-események, és a tevékenység napló panel is található. Szolgáltatás állapotával kapcsolatos értesítésekre tájékoztató vagy hajtható végre attól függően, hogy az osztály is lehet.

A szolgáltatás állapotával kapcsolatos értesítésekre öt osztályait van:  

- **Beavatkozás szükséges:** időről időre azt tapasztalhatja, valami szokatlan fordulhat elő, a fiókban. Előfordulhat, hogy az Ön ennek orvoslása érdekében kell. Meg kell végrehajtani a műveleteket vagy részletező értesítést kapni, vagy kérje az Azure mérnöki vagy a támogatási tájékoztatást.  
- **Támogatott helyreállítási:** olyan esemény történt, és a mérnökök megerősítette, hogy továbbra is problémát hatása. Mérnöki csapathoz kell Önnek közvetlenül a szolgáltatások kerüljön visszaállítása.  
- **Incidens:** esemény érintő szolgáltatás egy vagy több erőforrást az előfizetésében jelenleg érinti.  
- **Karbantartás:** ezt az egy értesítést a tájékoztat a tervezett karbantartások tevékenység, amely hatással lehet az előfizetéshez tartozó erőforrások közül legalább egyet.  
- **Információ:** az idő időre is küldünk értesítéseket meg, amely növelheti az erőforrás-használat lehetséges optimalizálásokat kapcsolatos Önnek egy kommunikáljon.  
- **Biztonsági:** sürgős biztonsági az Azure-on futó solution(s) vonatkozó információkat.

Minden szolgáltatás állapotának értesítés fogja hajtani a hatókör és az erőforrások hatással van. Részletek tartalmazza:

Tulajdonság neve | Leírás
-------- | -----------
csatornák | A következő értékek egyike: "Rendszergazda", "Művelet"
correlationId | Az általában egy GUID, amely a karakterlánc-formátum. Ugyanaz a uber művelet tartozik, amely az események az azonos correlationId általában megosztani.
eventDataId | Az esemény egyedi azonosítója
EventName | Az esemény címe
szint | Az esemény szintje. A következő értékek egyikét: "Kritikus", "Error", "Figyelmeztetés", "Tájékoztató" és "Részletes"
resourceProviderName | Az erőforrás-szolgáltató az érintett erőforrás neve
a resourceType| Az erőforrástípust az érintett erőforrás
a részállapot | Általában a megfelelő REST HTTP-állapotkód: hívja, de más a részállapot, például a gyakori értékek leíró karakterláncok is használható: OK (HTTP-állapotkód:: 200), létrehozott (HTTP-állapotkód:: 201-es), fogadja el (HTTP-állapotkód:: 202), nem tartalom (HTTP-állapotkód: 204), hibás kérés (HTTP-állapotkód:: 400), nem található (HTTP-állapotkód: 404-es), ütközés (HTTP-állapotkód: : 409), belső kiszolgálóhiba (HTTP-állapotkód: 500), a Service nem érhető el (HTTP-állapotkód: 503-as), átjáró időtúllépése (HTTP-állapotkód: 504).
eventTimestamp | Az esemény az esemény megfelelő a kérés feldolgozása az Azure-szolgáltatás által kiváltott idejét jelző időbélyegző.
submissionTimestamp |   Az esemény váltak elérhetővé lekérdezése idejét jelző időbélyegző.
subscriptionId | Az Azure-előfizetés, amelyben ez az esemény naplózásának
status | A művelet állapotát leíró karakterlánc. Néhány gyakori értékek a következők: folyamatban van, sikeres, sikertelen, aktív, a lépések megoldva.
operationName | A művelet neve.
category | "ServiceHealth"
resourceId | Erőforrás-azonosító az érintett erőforrás.
Properties.Title | Ez a kommunikáció honosított címét. Angol az alapértelmezett nyelv.
Properties.Communication | A HTML-kódot szolgáltatással való kommunikációhoz honosított részleteit. Angol az alapértelmezett beállítás.
Properties.incidentType | A lehetséges értékek: AssistedRecovery, ActionRequired, információk, incidens, karbantartási, biztonsági
Properties.trackingId | Ez az esemény társítva van az incidens azonosítja. Ezzel az incidensekhez kapcsolódó eseményeket összefüggéseket.
Properties.impactedServices | Az escape-karakterrel megjelölt JSON-blob a szolgáltatások és az incidens által érintett területek leíró. Szolgáltatások listáját, amelyek mindegyike rendelkezik egy szolgáltatásnév és ImpactedRegions, amelyek mindegyikének a RegionName listáját.
Properties.defaultLanguageTitle | A kommunikáció, angol nyelven
Properties.defaultLanguageContent | A kommunikáció vagy a html-kódot, vagy az egyszerű szöveg angol nyelven
Properties.Stage | AssistedRecovery, a ActionRequired, a információkat, az incidensek, a biztonsági a lehetséges értékek: aktív, amelyek megoldva. A karbantartás vannak: aktív, tervezett, esetbejegyzések, visszavonva, Rescheduled, megoldva, kész
Properties.communicationId | A kommunikáció ezt az eseményt hozzá rendelve.


## <a name="viewing-your-service-health-notifications-in-the-azure-portal"></a>A szolgáltatás állapotával kapcsolatos értesítésekre jeleníti meg az Azure-portálon
1.  Az a [portal](https://portal.azure.com), keresse meg a **figyelő** szolgáltatás

    ![Figyelés](./media/monitoring-service-notifications/home-monitor.png)
2.  Kattintson a **figyelő** elemmel nyithatja meg a a figyelő panelen. Ez a panel egyetlen, összevont nézetben jeleníti meg az összes figyelési beállítást és adatot. A panel az első alkalommal a **Tevékenységnapló** területet megjelenítve nyílik meg.

3.  Most kattintson a **szolgáltatáshoz értesítést** szakasz

    ![Figyelés](./media/monitoring-service-notifications/service-health-summary.png)
4.  Az egyes megtekintheti annak további részleteit a sor elemek

5. Kattintson a **+ Hozzáadás tevékenység napló riasztási** művelet értesítéseket annak érdekében, hogy az ilyen típusú jövőbeli szolgáltatási értesítésekhez értesítést kap. További riasztások konfigurálása a szolgáltatáshoz értesítést a [kattintson ide](monitoring-activity-log-alerts-on-service-notifications.md)

## <a name="next-steps"></a>További lépések:
Fogadási [riasztási értesítések, amikor az állapotfigyelő szolgáltatáshoz értesítést](monitoring-activity-log-alerts-on-service-notifications.md) van közzétéve.  
További információ [tevékenységriasztásokat napló](monitoring-activity-log-alerts.md)
