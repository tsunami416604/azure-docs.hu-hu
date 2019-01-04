---
title: Az Azure digitális Twins figyelésének konfigurálása |} A Microsoft Docs
description: Útmutató az Azure digitális Twins figyelésének konfigurálása.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/26/2018
ms.author: adgera
ms.custom: seodec18
ms.openlocfilehash: 2749a5c6c4e6003c51523d83c46b48d3b55b3d45
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/28/2018
ms.locfileid: "53807584"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>Az Azure digitális Twins figyelésének konfigurálása

Az Azure digitális Twins támogatja a hatékony naplózás, figyelés és elemzés. Megoldások fejlesztők használhatják az Azure Log Analytics, a diagnosztikai naplók, tevékenység naplózása és más szolgáltatások egy IoT-alkalmazás összetett figyelési igényeinek támogatása. Naplózási beállítások kombinálhatók, kérdezheti le vagy rekordokat jelenít meg több szolgáltatás és a részletes naplózást biztosít számos szolgáltatás számára.

Ez a cikk összefoglalja a naplózás és figyelés lehetőségeket és az Azure digitális Twins meghatározott módokon ötvözheti őket.

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

    ![Tevékenységnapló][1]

A speciális naplózása:

1. Válassza ki a **naplók** lehetőséget a megjelenítéséhez a **Tevékenységnaplók elemzésének áttekintése**:

    ![Kiválasztás][2]

1. A **Tevékenységnaplók elemzésének áttekintése** alapvető tevékenységnapló adatainak foglalja össze:

    ![Tevékenységnaplók elemzésének áttekintése][3]

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
1. Kattintson a **diagnosztikai beállítások**:

    ![Diagnosztikai beállítások egy][4]

1. Kattintson a **diagnosztika bekapcsolása** adatgyűjtéshez (Ha korábban nem engedélyezve).
1. Töltse ki a kívánt mezőket, és válassza ki, hogyan és hol adatok lesznek mentve:

    ![Diagnosztikai beállítások két][5]

    Diagnosztikai naplók gyakran lesznek mentve, használatával [Azure File Storage](../storage/files/storage-files-deployment-guide.md) és a megosztott [Azure Log Analytics](../azure-monitor/log-query/get-started-portal.md). Mindkét lehetőség választható.

>[!TIP]
>Használat **diagnosztikai naplók** az erőforrás-műveletek betekintést.

## <a name="azure-monitor-and-log-analytics"></a>Az Azure monitor és a log analytics

IoT-alkalmazások különböző források, eszközök, helyek és adatok egyesítése egy. Részletes naplózás minden egyes adott adatrészletet, szolgáltatás vagy összetevő a teljes alkalmazás-architektúra részletes információkat tartalmaz, de egy egységes áttekintést gyakran szükség, karbantartási és a hibakereséshez.

Az Azure Monitor magában foglalja a hatékony Log Analytics szolgáltatást, amely lehetővé teszi, hogy a naplózás forrásból tekintheti meg és egyetlen helyen elemezheti. Az Azure Monitor hasznos így nagyon kifinomult IoT-alkalmazásokban található naplók elemzése.

Használati példák:

* Több diagnosztikai napló előzményeket lekérdezése
* Több felhasználó által definiált függvény naplóinak megtekintése
* Egy megadott időkereten belül két vagy több szolgáltatás naplóinak megjelenítése

Teljes napló lekérdezése által biztosított [Azure Log Analytics](../azure-monitor/log-query/log-query-overview.md). Ezek a hatékony szolgáltatások beállítása:

1. Keresse meg **Log Analytics** az Azure Portalon.
1. Láthatja, hogy a rendelkezésre álló **Log Analytics** példányok. Válasszon egyet, és válassza ki **naplók** lekérdezéséhez:

    ![Log Analytics][6]

1. Ha még nem rendelkezik egy **Log Analytics** példányt hozhat létre egy munkaterületet kattintva a **Hozzáadás** gombra:

    ![OMS-létrehozásához][7]

Miután a **Log Analytics** példány üzembe van helyezve, hatékony lekérdezések használatával bejegyzések találhatók az Többszörösök vagy a keresés használatával adott feltétel használatával **Naplókezelés**:

   ![Naplókezelés][8]

Hatékony lekérdezési műveletekkel kapcsolatos további információkért lásd: [Ismerkedés a lekérdezések](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> Események küldése során egy 5 perces késleltetés tapasztalhat **Log Analytics** először.

Az Azure Log Analytics is biztosít a nagy teljesítményű hiba- és riasztási értesítések szolgáltatásokat, amelyek kattintva tekinthet meg **diagnosztizálása és a problémák megoldásához**:

   ![Figyelmeztetés és hiba értesítések][9]

>[!TIP]
>Használat **Log Analytics** a lekérdezési napló előzményeket több alkalmazás funkcióit, előfizetések és szolgáltatások.

## <a name="other-options"></a>Egyéb beállítások

Az Azure digitális Twins is támogatja az alkalmazás-specifikus naplózás és a biztonsági naplózás. Az Azure digitális Twins példány számára elérhető összes Azure naplózási beállítások alapos áttekintéséért lásd: a [az Azure log naplózási](../security/azure-log-audit.md) cikk.

## <a name="next-steps"></a>További lépések

- További tudnivalók az Azure [tevékenységeket tartalmazó naplók](../azure-monitor/platform/activity-logs-overview.md).

- Részletesen mélyebb Azure diagnosztikai beállítások olvassa el az [diagnosztikai naplók áttekintése](../azure-monitor/platform/diagnostic-logs-overview.md).

- Tudjon meg többet [Azure Log Analytics](../azure-monitor/log-query/get-started-portal.md).

<!-- Images -->
[1]: media/how-to-configure-monitoring/activity-log.png
[2]: media/how-to-configure-monitoring/activity-log-select.png
[3]: media/how-to-configure-monitoring/log-analytics-overview.png
[4]: media/how-to-configure-monitoring/diagnostic-settings-one.png
[5]: media/how-to-configure-monitoring/diagnostic-settings-two.png
[6]: media/how-to-configure-monitoring/log-analytics.png
[7]: media/how-to-configure-monitoring/log-analytics-oms.png
[8]: media/how-to-configure-monitoring/log-analytics-management.png
[9]: media/how-to-configure-monitoring/log-analytics-notifications.png
