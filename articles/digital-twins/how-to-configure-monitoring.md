---
title: A figyelés konfigurálása - Azure Digital Twins | Microsoft dokumentumok
description: Ismerje meg, hogyan konfigurálhatja az Azure Digital Twins figyelési és naplózási beállításait.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: e35e18be20af3bd9f6fdc9541f9abfe857a6b87c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511858"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>A figyelés konfigurálása az Azure Digital Twins-ben

Az Azure Digital Twins támogatja a robusztus naplózást, figyelést és elemzést. A megoldások fejlesztői az Azure Monitor-naplók, diagnosztikai naplók, tevékenységnaplózás és egyéb szolgáltatások segítségével támogathatják az IoT-alkalmazások összetett figyelési igényeit. A naplózási beállítások kombinálhatók a rekordok lekérdezéséhez vagy megjelenítéséhez számos szolgáltatásban, és részletes naplózási lefedettséget biztosíthatszámos szolgáltatás számára.

Ez a cikk összefoglalja a naplózási és figyelési lehetőségeket, és hogyan kombinálhatja őket az Azure Digital Twins-re jellemző módon.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="review-activity-logs"></a>Tevékenységnaplók áttekintése

Az [Azure-tevékenységnaplók](../azure-monitor/platform/platform-logs-overview.md) gyors betekintést nyújtanak az egyes Azure-szolgáltatáspéldányok előfizetési szintű esemény- és működési előzményeibe.

Az előfizetési szintű események a következők:

* Erőforrás létrehozása
* Erőforrás eltávolítása
* Alkalmazástitkok létrehozása
* Integráció más szolgáltatásokkal

Az Azure Digital Twins tevékenységnaplózása alapértelmezés szerint engedélyezve van, és az Azure Portalon keresztül érhető el:

1. Az Azure Digital Twins-példány kiválasztása.
1. A megjelenítési panel megjelenítéséhez válassza a **Tevékenységnaplót:**

    [![Tevékenységnapló](media/how-to-configure-monitoring/activity-log.png)](media/how-to-configure-monitoring/activity-log.png#lightbox)

Speciális tevékenységnaplózáshoz:

1. Válassza a **Naplók** lehetőséget a **Tevékenységnapló-elemzés áttekintésének megjelenítéséhez:**

    [![Kiválasztás](media/how-to-configure-monitoring/activity-log-select.png)](media/how-to-configure-monitoring/activity-log-select.png#lightbox)

1. A **Tevékenységnapló-elemzés áttekintése** összefoglalja az alapvető tevékenységnapló-adatokat:

    [![Tevékenységnapló-elemzés – áttekintés]( media/how-to-configure-monitoring/log-analytics-overview.png)]( media/how-to-configure-monitoring/log-analytics-overview.png#lightbox)

>[!TIP]
>A **tevékenységnaplók** segítségével gyors betekintést nyerhet az előfizetési szintű eseményekbe.

## <a name="enable-customer-diagnostic-logs"></a>Ügyféldiagnosztikai naplók engedélyezése

Az Azure [diagnosztikai beállításait](../azure-monitor/platform/platform-logs-overview.md) be lehet állítani az egyes Azure-példányok a tevékenység naplózásának kiegészítésére. Míg a tevékenységnaplók előfizetési szintű eseményekre vonatkoznak, a diagnosztikai naplózás betekintést nyújt az erőforrások működési előzményeibe.

A diagnosztikai naplózás példái a következők:

* Egy felhasználó által definiált függvény végrehajtási ideje
* Egy sikeres API-kérelem válaszállapot-kódja
* Alkalmazáskulcs beolvasása tárolóból

Egy példány diagnosztikai naplóinak engedélyezése:

1. Az erőforrás az Azure Portalon.
1. Válassza **a Diagnosztikai beállítások lehetőséget:**

    [![Diagnosztikai beállítások egy](media/how-to-configure-monitoring/diagnostic-settings-one.png)](media/how-to-configure-monitoring/diagnostic-settings-one.png#lightbox)

1. Adatok gyűjtéséhez válassza **a Diagnosztika bekapcsolása** lehetőséget (ha korábban nem engedélyezve van).
1. Töltse ki a kért mezőket, és válassza ki az adatok mentésének módját és helyét:

    [![Diagnosztikai beállítások két](media/how-to-configure-monitoring/diagnostic-settings-two.png)](media/how-to-configure-monitoring/diagnostic-settings-two.png#lightbox)

    A diagnosztikai naplókat gyakran menti az [Azure File Storage](../storage/files/storage-files-deployment-guide.md) használatával, és megosztják az Azure Monitor [naplóival.](../azure-monitor/log-query/get-started-portal.md) Mindkét lehetőség kiválasztható.

>[!TIP]
>Diagnosztikai **naplók** használatával betekintést az erőforrás-műveletek.

## <a name="azure-monitor-and-log-analytics"></a>Azure-figyelő- és naplóelemzés

Az IoT-alkalmazások egyesítik a különböző erőforrásokat, eszközöket, helyeket és adatokat egybe. A részletes naplózás részletes információkat nyújt a teljes alkalmazásarchitektúra minden egyes darabjáról, szolgáltatásáról vagy összetevőjéről, de a karbantartáshoz és a hibakereséshez gyakran egységes áttekintésre van szükség.

Az Azure Monitor tartalmazza a hatékony naplóelemzési szolgáltatást, amely lehetővé teszi a naplózási források megtekintését és elemzését egy helyen. Az Azure Monitor ezért rendkívül hasznos a naplók elemzéséhez a kifinomult IoT-alkalmazásokban.

A felhasználás példái a következők:

* Több diagnosztikai naplóelőzmény lekérdezése
* Több felhasználó által definiált függvény naplóinak megtekintéséhez
* Két vagy több szolgáltatás naplóinak megjelenítése egy adott időkereten belül

A teljes naplólekérdezés az [Azure Monitor naplóin](../azure-monitor/log-query/log-query-overview.md)keresztül érhető el. A hatékony funkciók beállítása:

1. Keressen rá a **Log Analytics** kifejezésre az Azure Portalon.
1. Megjelenik a rendelkezésre álló **Log Analytics-munkaterületi** példányok. Válasszon egyet, és válassza a Lekérdezéshez a **Naplók** lehetőséget:

    [![Naplóelemzés](media/how-to-configure-monitoring/log-analytics.png)](media/how-to-configure-monitoring/log-analytics.png#lightbox)

1. Ha még nem rendelkezik **Log Analytics-munkaterületi** példával, a **Hozzáadás** gombra kattintva létrehozhat munkaterületet:

    [![OMS létrehozása](media/how-to-configure-monitoring/log-analytics-oms.png)](media/how-to-configure-monitoring/log-analytics-oms.png#lightbox)

A **Log Analytics-munkaterületi** példány kiépítése után hatékony lekérdezésekkel kereshet bejegyzéseket a többszörösnaplókban, vagy konkrét feltételek kel kereshet a **Log Management**használatával:

   [![Naplókezelés](media/how-to-configure-monitoring/log-analytics-management.png)](media/how-to-configure-monitoring/log-analytics-management.png#lightbox)

A hatékony lekérdezési műveletekről további információt a [lekérdezések első lépései](../azure-monitor/log-query/get-started-queries.md)című olvasása című ekben talál.

> [!NOTE]
> 5 perces késést tapasztalhat, amikor először küld eseményeket a **Log Analytics-munkaterületre.**

Az Azure Monitor naplói hatékony hiba- és riasztási értesítési szolgáltatásokat is nyújtanak, amelyek a **Problémák diagnosztizálása és megoldása**lehetőség kiválasztásával tekinthetők meg:

   [![Riasztási és hibaértesítések](media/how-to-configure-monitoring/log-analytics-notifications.png)](media/how-to-configure-monitoring/log-analytics-notifications.png#lightbox)

>[!TIP]
>A **Log Analytics-munkaterület** használatával több alkalmazásfunkció, előfizetés vagy szolgáltatás naplóelőzményeinek lekérdezésére is lekérdezheti a naplóelőzményeket.

## <a name="other-options"></a>Egyéb lehetőségek

Az Azure Digital Twins alkalmazásspecifikus naplózást és biztonsági naplózást is támogat. Az Azure Digital Twins-példány számára elérhető összes Azure naplózási lehetőség alapos áttekintéséhez olvassa el az [Azure-napló naplózási cikkét.](../security/fundamentals/log-audit.md)

## <a name="next-steps"></a>További lépések

- További információ az Azure [tevékenységnaplóiról.](../azure-monitor/platform/platform-logs-overview.md)

- A diagnosztikai naplók áttekintésének olvasásával mélyebbre hatolhat az Azure diagnosztikai [beállításaiban.](../azure-monitor/platform/platform-logs-overview.md)

- További információ az [Azure Monitor naplóiról.](../azure-monitor/log-query/get-started-portal.md)
