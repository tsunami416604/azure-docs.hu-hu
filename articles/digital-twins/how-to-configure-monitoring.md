---
title: Az Azure digitális Twins figyelésének konfigurálása |} A Microsoft Docs
description: Útmutató az Azure digitális Twins figyelésének konfigurálása.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: v-adgera
ms.custom: seodec18
ms.openlocfilehash: 5dc2587a0c127106d5afb41e20eca43919065f1b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67118787"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>Az Azure digitális Twins figyelésének konfigurálása

Az Azure digitális Twins támogatja a hatékony naplózás, figyelés és elemzés. Megoldások fejlesztők használhatják az Azure Monitor naplókat, a diagnosztikai naplók, tevékenység naplózása és más szolgáltatások egy IoT-alkalmazás összetett figyelési igényeinek támogatása. Naplózási beállítások kombinálhatók, kérdezheti le vagy rekordokat jelenít meg több szolgáltatás és a részletes naplózást biztosít számos szolgáltatás számára.

Ez a cikk összefoglalja a naplózás és figyelés lehetőségeket és az Azure digitális Twins meghatározott módokon ötvözheti őket.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="review-activity-logs"></a>A Tevékenységnaplók áttekintése

Azure [tevékenységeket tartalmazó naplók](../azure-monitor/platform/activity-logs-overview.md) egyes Azure-szolgáltatás-példányokhoz tartozó előfizetés-szintű esemény és művelet előzményeket gyors betekintést nyújtson.

Előfizetés-szintű eseményeit tartalmazza:

* Erőforrás-létrehozás
* Erőforrás eltávolítása
* Alkalmazás titkos kulcsok létrehozása
* Más szolgáltatások integrálása

Az Azure digitális Twins naplózása alapértelmezés szerint engedélyezve van, és által az Azure Portalon tekintheti meg:

1. Az Azure digitális Twins-példány kiválasztása.
1. Választás **tevékenységnapló** viszi, megjelenik a megjelenítési panelen:

    [![Tevékenységnapló](media/how-to-configure-monitoring/activity-log.png)](media/how-to-configure-monitoring/activity-log.png#lightbox)

A speciális naplózása:

1. Válassza ki a **naplók** lehetőséget a megjelenítéséhez a **Tevékenységnaplók elemzésének áttekintése**:

    [![Kijelölés](media/how-to-configure-monitoring/activity-log-select.png)](media/how-to-configure-monitoring/activity-log-select.png#lightbox)

1. A **Tevékenységnaplók elemzésének áttekintése** alapvető tevékenységnapló adatainak foglalja össze:

    [![Tevékenységnaplók elemzésének áttekintése]( media/how-to-configure-monitoring/log-analytics-overview.png)]( media/how-to-configure-monitoring/log-analytics-overview.png#lightbox)

>[!TIP]
>Használat **tevékenységeket tartalmazó naplók** előfizetés-szintű események gyors elemzéseit.

## <a name="enable-customer-diagnostic-logs"></a>Ügyfél-diagnosztikai naplók engedélyezése

Azure [diagnosztikai beállítások](../azure-monitor/platform/diagnostic-logs-overview.md) állítható be az egyes Azure-beli példány lehetőséget a tevékenység naplózása. Tevékenységnaplók előfizetés-szintű eseményeit is vonatkozik, míg a diagnosztikai naplózás révén betekintést kaphat maguk az erőforrások működési előzményeit.

Diagnosztikai naplózás közé:

* A végrehajtás ideje, a felhasználó által definiált függvények
* A válaszként kapott állapotkód sikeres API-kérelem
* Egy tárolóból egy alkalmazáskulcsot beolvasása

Egy példánya számára a diagnosztikai naplók engedélyezése:

1. Nyissa meg az erőforrást az Azure Portalon.
1. Válassza ki **diagnosztikai beállítások**:

    [![Diagnosztikai beállítások egy](media/how-to-configure-monitoring/diagnostic-settings-one.png)](media/how-to-configure-monitoring/diagnostic-settings-one.png#lightbox)

1. Válassza ki **diagnosztika bekapcsolása** adatgyűjtéshez (Ha korábban nem engedélyezve).
1. Töltse ki a kívánt mezőket, és válassza ki, hogyan és hol adatok lesznek mentve:

    [![Diagnosztikai beállítások két](media/how-to-configure-monitoring/diagnostic-settings-two.png)](media/how-to-configure-monitoring/diagnostic-settings-two.png#lightbox)

    Diagnosztikai naplók gyakran lesznek mentve, használatával [Azure File Storage](../storage/files/storage-files-deployment-guide.md) és a megosztott [naplózza az Azure Monitor](../azure-monitor/log-query/get-started-portal.md). Mindkét lehetőség választható.

>[!TIP]
>Használat **diagnosztikai naplók** az erőforrás-műveletek betekintést.

## <a name="azure-monitor-and-log-analytics"></a>Az Azure monitor és a log analytics

IoT-alkalmazások különböző források, eszközök, helyek és adatok egyesítése egy. Részletes naplózás minden egyes adott adatrészletet, szolgáltatás vagy összetevő a teljes alkalmazás-architektúra részletes információkat tartalmaz, de egy egységes áttekintést gyakran szükség, karbantartási és a hibakereséshez.

Az Azure Monitor magában foglalja a hatékony log analytics szolgáltatást, amely lehetővé teszi, hogy a naplózás forrásból tekintheti meg és egyetlen helyen elemezheti. Az Azure Monitor hasznos így nagyon kifinomult IoT-alkalmazásokban található naplók elemzése.

Használati példák:

* Több diagnosztikai napló előzményeket lekérdezése
* Több felhasználó által definiált függvény naplóinak megtekintése
* Egy megadott időkereten belül két vagy több szolgáltatás naplóinak megjelenítése

Teljes napló lekérdezése által biztosított [naplózza az Azure Monitor](../azure-monitor/log-query/log-query-overview.md). Ezek a hatékony szolgáltatások beállítása:

1. Keresse meg **Log Analytics** az Azure Portalon.
1. Láthatja, hogy a rendelkezésre álló **Log Analytics-munkaterület** példányok. Válasszon egyet, és válassza ki **naplók** lekérdezéséhez:

    [![A log analytics](media/how-to-configure-monitoring/log-analytics.png)](media/how-to-configure-monitoring/log-analytics.png#lightbox)

1. Ha még nem rendelkezik egy **Log Analytics-munkaterület** példány, létrehozhat egy munkaterület kiválasztásával a **Hozzáadás** gombra:

    [![OMS-létrehozásához](media/how-to-configure-monitoring/log-analytics-oms.png)](media/how-to-configure-monitoring/log-analytics-oms.png#lightbox)

Egyszer a **Log Analytics-munkaterület** példány üzembe van helyezve, hatékony lekérdezések használatával bejegyzések találhatók az Többszörösök vagy a keresés használatával adott feltétel használatával **Naplókezelés**:

   [![Naplókezelés](media/how-to-configure-monitoring/log-analytics-management.png)](media/how-to-configure-monitoring/log-analytics-management.png#lightbox)

Hatékony lekérdezési műveletekkel kapcsolatos további információkért lásd: [Ismerkedés a lekérdezések](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> Események küldése során egy 5 perces késleltetés tapasztalhat **Log Analytics-munkaterület** először.

Az Azure Monitor naplóira is biztosítanak a hatékony hiba- és riasztási értesítések szolgáltatásokat, amelyek kijelölésével tekinthet meg **diagnosztizálása és a problémák megoldásához**:

   [![Figyelmeztetés és hiba értesítések](media/how-to-configure-monitoring/log-analytics-notifications.png)](media/how-to-configure-monitoring/log-analytics-notifications.png#lightbox)

>[!TIP]
>Használat **Log Analytics-munkaterület** a lekérdezési napló előzményeket több alkalmazás funkcióit, előfizetések és szolgáltatások.

## <a name="other-options"></a>Egyéb beállítások

Az Azure digitális Twins is támogatja az alkalmazás-specifikus naplózás és a biztonsági naplózás. Az Azure digitális Twins példány számára elérhető összes Azure naplózási beállítások alapos áttekintéséért lásd: a [az Azure log naplózási](../security/azure-log-audit.md) cikk.

## <a name="next-steps"></a>További lépések

- További tudnivalók az Azure [tevékenységeket tartalmazó naplók](../azure-monitor/platform/activity-logs-overview.md).

- Részletesen mélyebb Azure diagnosztikai beállítások olvassa el az [diagnosztikai naplók áttekintése](../azure-monitor/platform/diagnostic-logs-overview.md).

- Tudjon meg többet [naplózza az Azure Monitor](../azure-monitor/log-query/get-started-portal.md).
