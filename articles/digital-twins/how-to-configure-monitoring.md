---
title: A monitorozás konfigurálása az Azure digitális Twins szolgáltatásban | Microsoft Docs
description: A monitorozás konfigurálása az Azure digitális Twins szolgáltatásban.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 08/09/2019
ms.author: v-adgera
ms.custom: seodec18
ms.openlocfilehash: dd1481c74cdf519dcb4588e0b7cf09b7b6ab5f1d
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950128"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>A monitorozás konfigurálása az Azure digitális Twins szolgáltatásban

Az Azure digitális Twins támogatja a robusztus naplózást, monitorozást és elemzést. A megoldások fejlesztői Azure Monitor naplókat, diagnosztikai naplókat, tevékenység-naplózást és más szolgáltatásokat használhatnak a IoT-alkalmazások összetett figyelési igényeinek támogatásához. A naplózási lehetőségek kombinálhatók több szolgáltatás rekordjainak lekérdezéséhez és megjelenítéséhez, valamint számos szolgáltatás részletes naplózási lefedettségének biztosításához.

Ez a cikk összefoglalja a naplózási és figyelési lehetőségeket, valamint azt, hogyan kombinálhatja őket az Azure digitális Twins-ra jellemző módokon.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="review-activity-logs"></a>Tevékenységek naplóinak áttekintése

Az Azure- [Tevékenységnaplók](../azure-monitor/platform/activity-logs-overview.md) gyors elemzéseket nyújtanak az előfizetési szintű eseményekről és a műveleti előzményekről az egyes Azure-szolgáltatási példányok esetében.

Az előfizetés szintű események a következők:

* Erőforrás-létrehozás
* Erőforrás-eltávolítás
* Alkalmazás-titkok létrehozása
* Integrálás más szolgáltatásokkal

Az Azure Digital Twins tevékenység-naplózása alapértelmezés szerint engedélyezve van, és a Azure Portal a következő módon érhető el:

1. Válassza ki az Azure Digital Twins-példányt.
1. A megjelenítési panel felépítéséhez válassza a **műveletnapló** elemet:

    [![Műveletnapló](media/how-to-configure-monitoring/activity-log.png)](media/how-to-configure-monitoring/activity-log.png#lightbox)

A speciális tevékenységek naplózása:

1. Válassza a **naplók** lehetőséget a **Activity log Analytics áttekintésének**megjelenítéséhez:

    [![Kijelölés](media/how-to-configure-monitoring/activity-log-select.png)](media/how-to-configure-monitoring/activity-log-select.png#lightbox)

1. A **Activity log Analytics áttekintése** összefoglalja az alapvető tevékenység naplójának adatait:

    [![A Activity log Analytics áttekintése]( media/how-to-configure-monitoring/log-analytics-overview.png)]( media/how-to-configure-monitoring/log-analytics-overview.png#lightbox)

>[!TIP]
>A **Tevékenységnaplók** használatával gyors elemzéseket készíthet az előfizetési szintű eseményekről.

## <a name="enable-customer-diagnostic-logs"></a>Az ügyfél diagnosztikai naplóinak engedélyezése

Az Azure [diagnosztikai beállításai](../azure-monitor/platform/diagnostic-logs-overview.md) mindegyik Azure-példányhoz megadhatók a tevékenységek naplózásának kiegészítéséhez. Míg a tevékenység naplói előfizetési szintű eseményekre vonatkoznak, a diagnosztikai naplózási szolgáltatás betekintést nyújt az erőforrások működési előzményeibe.

A diagnosztikai naplózás példái például a következők:

* Felhasználó által definiált függvény végrehajtási ideje
* Egy sikeres API-kérelem válasz-állapotkód
* Alkalmazás-kulcs lekérése egy tárból

Diagnosztikai naplók engedélyezése egy példányhoz:

1. Hozza létre az erőforrást Azure Portal.
1. **Diagnosztikai beállítások**kiválasztása:

    [![Diagnosztikai beállítások – egy](media/how-to-configure-monitoring/diagnostic-settings-one.png)](media/how-to-configure-monitoring/diagnostic-settings-one.png#lightbox)

1. Jelölje be **a diagnosztika** bekapcsolása az adatok gyűjtéséhez (ha korábban nem engedélyezett) lehetőséget.
1. Adja meg a kért mezőket, és válassza ki, hogyan és hol lesznek mentve az adat:

    [![Diagnosztikai beállítások – kettő](media/how-to-configure-monitoring/diagnostic-settings-two.png)](media/how-to-configure-monitoring/diagnostic-settings-two.png#lightbox)

    A diagnosztikai naplókat gyakran az [Azure file Storage](../storage/files/storage-files-deployment-guide.md) és az [Azure monitor-naplók](../azure-monitor/log-query/get-started-portal.md)megosztva használják. Mindkét lehetőség kiválasztható.

>[!TIP]
>**Diagnosztikai naplók** használatával elemzéseket készíthet az erőforrás-műveletekről.

## <a name="azure-monitor-and-log-analytics"></a>Azure monitor és log Analytics

A IoT-alkalmazások különálló erőforrásokat, eszközöket, helyszíneket és adategységeket egyesítenek. A részletes naplózás részletes információkkal szolgál a teljes alkalmazás-architektúra egyes elemeiről, szolgáltatásairól vagy összetevőiről, de a karbantartáshoz és a hibakereséshez gyakran szükség van egy egységes áttekintésre.

Azure Monitor tartalmazza a hatékony log Analytics szolgáltatást, amely lehetővé teszi a naplózási források megtekintését és elemzését egy helyen. A Azure Monitor ezért nagyon hasznos a naplók kifinomult IoT-alkalmazásokon belüli elemzéséhez.

Példák a következőkre:

* Több diagnosztikai napló előzményeinek lekérdezése
* Több felhasználó által definiált függvény naplóinak megtekintése
* Két vagy több szolgáltatás naplóinak megjelenítése egy adott időkereten belül

A teljes naplózási lekérdezéseket [Azure monitor naplókon](../azure-monitor/log-query/log-query-overview.md)keresztül biztosítjuk. A következő hatékony funkciók beállítása:

1. **Log Analytics** keresése a Azure Portalban.
1. Megjelenik az elérhető **log Analytics munkaterület** -példányok. Válasszon egyet, és válassza ki a lekérdezni kívánt naplókat:

    [![Log Analytics](media/how-to-configure-monitoring/log-analytics.png)](media/how-to-configure-monitoring/log-analytics.png#lightbox)

1. Ha még nem rendelkezik **log Analytics munkaterület** -példánnyal, létrehozhat egy munkaterületet az **Add (Hozzáadás** ) gomb kiválasztásával:

    [![OMS létrehozása](media/how-to-configure-monitoring/log-analytics-oms.png)](media/how-to-configure-monitoring/log-analytics-oms.png#lightbox)

A **log Analytics munkaterület** -példány üzembe helyezése után hatékony lekérdezéseket használhat a több naplóban található bejegyzések kereséséhez, vagy adott feltételek alapján kereshet a **naplózási**szolgáltatás használatával:

   [![Naplózás kezelése](media/how-to-configure-monitoring/log-analytics-management.png)](media/how-to-configure-monitoring/log-analytics-management.png#lightbox)

A hatékony lekérdezési műveletekkel kapcsolatos további információkért lásd: [Bevezetés a lekérdezések](../azure-monitor/log-query/get-started-queries.md)használatába.

> [!NOTE]
> Egy 5 perces késleltetést tapasztalhat, amikor első alkalommal küld eseményeket **log Analytics** munkaterületre.

Azure Monitor a naplók hatékony és riasztási értesítési szolgáltatásokat is biztosítanak, amelyek megtekinthetők a **problémák diagnosztizálásával és megoldásával**:

   [![Riasztási és hibajelentési értesítések](media/how-to-configure-monitoring/log-analytics-notifications.png)](media/how-to-configure-monitoring/log-analytics-notifications.png#lightbox)

>[!TIP]
>Több alkalmazás-funkció, előfizetés vagy szolgáltatás esetén **log Analytics munkaterület** használatával kérdezheti le a napló előzményeit.

## <a name="other-options"></a>Egyéb beállítások

Az Azure Digital Twins az alkalmazásspecifikus naplózást és a biztonsági naplózást is támogatja. Az Azure-beli digitális Twins-példányok Azure-naplózási lehetőségeinek részletes áttekintését az [Azure napló naplózási](../security/fundamentals/log-audit.md) cikkében találja.

## <a name="next-steps"></a>További lépések

- További információ az Azure- [tevékenységek naplóiról](../azure-monitor/platform/activity-logs-overview.md).

- A [diagnosztikai naplók áttekintésével](../azure-monitor/platform/diagnostic-logs-overview.md)mélyebben megismerheti az Azure diagnosztikai beállításait.

- További információ a [Azure monitor naplókról](../azure-monitor/log-query/get-started-portal.md).
