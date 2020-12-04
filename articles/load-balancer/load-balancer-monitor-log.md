---
title: A nyilvános alapszintű Load Balancerhoz tartozó műveletek, események és számlálók figyelése
titleSuffix: Azure Load Balancer
description: Útmutató a Azure Load Balancer naplózásának engedélyezéséhez
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2020
ms.author: allensu
ms.openlocfilehash: 6742723e24df83ac8112e224f1999f116ab82c94
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2020
ms.locfileid: "96572779"
---
# <a name="azure-monitor-logs-for-the-standard-azure-load-balancer"></a>A standard Azure Load Balancer Azure Monitor naplófájljai

Az Azure-ban különféle típusú naplókat használhat a standard Load Balancerek felügyeletére és hibakeresésére. A naplók továbbíthatók egy Event hub-vagy Log Analytics-munkaterületre. Az összes napló kinyerhető az Azure Blob Storage-ból, és különböző eszközökön, például Excelben és Power BI is megtekinthető.  Az alábbi listából további információkat talál a naplók különböző típusairól.

* **Tevékenységek naplói:** A [megtekintési tevékenység naplói segítségével figyelheti az erőforrásokon végrehajtott műveleteket](../azure-resource-manager/management/view-activity-logs.md) , és megtekintheti az Azure-előfizetések és azok állapotát. A Tevékenységnaplók alapértelmezés szerint engedélyezve vannak, és a Azure Portal megtekinthetők. Ezek a naplók az alapszintű és a standard Load Balancerhez is elérhetők.
* **Standard Load Balancer metrikák:** Ezzel a naplóval lekérdezheti a standard Azure Load Balancer naplóiként exportált metrikákat. Ezek a naplók csak a standard terheléselosztó esetében érhetők el.

> [!IMPORTANT]
> **Az állapot-mintavételi és Load Balancer riasztási eseménynaplók jelenleg nem működőképesek, és a [Azure Load Balancer ismert problémái](whats-new.md#known-issues)vannak felsorolva.** 

> [!IMPORTANT]
> A naplók csak a Resource Manager-alapú üzemi modellben üzembe helyezett erőforrások esetében érhetők el. A klasszikus üzemi modell erőforrásaihoz nem használhat naplókat. További információ az üzembe helyezési modellekről: a [Resource Manager telepítésének ismertetése és a klasszikus üzembe helyezés](../azure-resource-manager/management/deployment-models.md).

## <a name="enable-logging"></a>Naplózás engedélyezése

A tevékenységnaplózás automatikusan engedélyezve van minden Resource Manager-erőforráshoz. Az esemény-és állapot-mintavételi naplózás engedélyezése a naplókon keresztül elérhető adatok gyűjtésének megkezdéséhez. A naplózás engedélyezéséhez kövesse az alábbi lépéseket.

Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Ha még nem rendelkezik terheléselosztó, a folytatás előtt [hozzon létre egy Load balancert](./quickstart-load-balancer-standard-public-portal.md) .

1. A portálon kattintson az **erőforráscsoportok** elemre.
2. Válassza ki **\<resource-group-name>** a terheléselosztó helyét.
3. Válassza ki a terheléselosztót.
4. Válassza a **műveletnapló**  >  **diagnosztikai beállítások** lehetőséget.
5. A **diagnosztika beállításai** ablaktábla **diagnosztika beállításai** területén válassza a **+ diagnosztikai beállítás hozzáadása** elemet.
6. A **diagnosztikai beállítások** létrehozása panelen adja meg a **MyLBDiagnostics** nevet a **név** mezőben.
7. A **diagnosztikai beállításoknak** három lehetősége van.  A követelmények közül választhat egyet, kettőt vagy mindháromat, és konfigurálhatja a következőt:
   * **Archiválás egy Storage-fiókba**
   * **Streamelés eseményközpontba**
   * **Küldés a Log Analyticsnek**

    ### <a name="archive-to-a-storage-account"></a>Archiválás tárfiókba
    Ehhez a folyamathoz már létre kell hoznia egy Storage-fiókot.  A Storage-fiók létrehozásával kapcsolatban tekintse meg [a Storage-fiók létrehozása](../storage/common/storage-account-create.md?tabs=azure-portal) című témakört.

    1. Jelölje be az archiválás elem melletti jelölőnégyzetet a **Storage-fiókhoz**.
    2. Válassza a **Konfigurálás** lehetőséget a **Storage-fiók kiválasztása** panel megnyitásához.
    3. Válassza ki azt az **előfizetést** , amelyben a Storage-fiókot létrehozták a legördülő listából.
    4. A legördülő listában válassza ki a Storage-fiók nevét a **Storage-fiók** területen.
    5. Kattintson az OK gombra.

    ### <a name="stream-to-an-event-hub"></a>Streamelés eseményközpontba
    Ehhez a folyamathoz már létrehozott Event hub szükséges.  Az Event hub létrehozásával kapcsolatban tekintse meg az [Event hub létrehozása Azure Portal használatával](../event-hubs/event-hubs-create.md) című témakört.

    1. Jelölje be a stream elem melletti jelölőnégyzetet az **Event hubhoz**
    2. A **Konfigurálás** elemre kattintva nyissa meg az **Event hub kiválasztása** ablaktáblát.
    3. Válassza ki azt az **előfizetést** , amelyben az Event hub létre lett hozva a legördülő listából.
    4. **Válassza ki az Event hub-névteret** a legördülő listából.
    5. **Válassza az Event hub-házirend neve elemet** a legördülő listából.
    6. Kattintson az OK gombra.

    ### <a name="send-to-log-analytics"></a>Küldés a Log Analyticsnek
    Ehhez a folyamathoz már létre kell hoznia és konfigurálnia kell egy log Analytics-munkaterületet.  Log Analytics munkaterület létrehozásával kapcsolatban lásd: [log Analytics munkaterület létrehozása a Azure Portal](../azure-monitor/learn/quick-create-workspace.md)

    1. Jelölje be a **küldés log Analyticsra** jelölőnégyzetet.
    2. Válassza ki azt az **előfizetést** , ahol a log Analytics munkaterület a legördülő mezőben található.
    3. Válassza ki a **log Analytics munkaterületet** a legördülő listából.


8.  A **diagnosztika beállításai** ablaktábla **metrika** szakasza alatt jelölje be a jelölőnégyzetet a következő mellett: **AllMetrics**

9. Győződjön meg arról, hogy minden helyes, és kattintson a **Mentés** gombra a **diagnosztikai beállítások** létrehozása ablaktábla tetején.

## <a name="activity-log"></a>Tevékenységnapló

A rendszer alapértelmezés szerint létrehozza a tevékenység naplóját. Konfigurálható úgy, hogy a [jelen cikk utasításait követve](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log)exportálható legyen az előfizetés szintjén. További információ ezekről a naplókról: a [tevékenység naplóinak megtekintése az erőforrásokkal kapcsolatos műveletek figyelése](../azure-resource-manager/management/view-activity-logs.md) című cikkben.

### <a name="view-and-analyze-the-activity-log"></a>A tevékenységnapló megtekintése és elemzése

A következő módszerek bármelyikével megtekintheti és elemezheti a tevékenység-naplózási adataikat:

* **Azure-eszközök:** A Azure PowerShellon, az Azure parancssori felületen (CLI), az Azure REST API vagy a Azure Portalon keresztül kérhet le adatokat a tevékenység naplóból. Az egyes módszerek részletes utasításait a [Resource Managerrel kapcsolatos naplózási műveletek](../azure-resource-manager/management/view-activity-logs.md) című cikkben találja.
* **Power bi:** Ha még nem rendelkezik [Power bi](https://powerbi.microsoft.com/pricing) -fiókkal, ingyenes kipróbáláshoz is kipróbálhatja. Az [Azure-naplók Power bi integrációját](https://powerbi.microsoft.com/integrations/azure-audit-logs/)használva az adatait előre konfigurált irányítópultokkal elemezheti, vagy testre szabhatja a nézeteket, hogy megfeleljenek a követelményeinek.

## <a name="metrics-as-logs"></a>Metrikák Naplókként
Ha metrikákat használ a Azure Monitor által biztosított exportálási funkciók naplózására, akkor exportálhatja Load Balancer metrikáit. Ezek a metrikák egy percenkénti mintavételi időközhöz tartozó naplóbejegyzést hoznak.

A naplók exportálásának mérőszáma erőforrás-szinten engedélyezve van. Ezeket a naplókat a diagnosztikai beállítások panelen, az erőforráscsoport alapján történő szűrés és a metrikák exportálásának engedélyezése Load Balancer kiválasztásával engedélyezheti. Ha a Load Balancer diagnosztikai beállítások oldal fel van választva, válassza a AllMetrics lehetőséget a jogosult metrikák naplókként való exportálásához.

Tekintse meg a jelen cikk [korlátozások](#limitations) című szakaszát a metrikus exportálási korlátozásokhoz.

### <a name="view-and-analyze-metrics-as-logs"></a>Metrikák megtekintése és elemzése naplókként
Miután engedélyezte a AllMetrics a standard Load Balancer diagnosztikai beállításaiban, ha az Event hub vagy Log Analytics munkaterületet használja, ezek a naplók a AzureMonitor táblában lesznek feltöltve. Ha tárolóba exportál, kapcsolódjon a Storage-fiókjához, és kérje le az esemény-és állapot-mintavételi naplók JSON-naplófájljait. A JSON-fájlok letöltése után konvertálhatja őket CSV formátumba, és megtekintheti az Excelben, Power BI vagy bármely más adatvizualizációs eszközön. 

> [!TIP]
> Ha ismeri a Visual Studiót, illetve C#-állandók és -változók módosításának alapfogalmait, használja a GitHubról elérhető [naplókonvertáló eszközöket](https://github.com/Azure-Samples/networking-dotnet-log-converter).

## <a name="stream-to-an-event-hub"></a>Streamelés eseményközpontba
Ha a diagnosztikai adatokat egy esemény központba továbbítják, akkor a központi naplózási elemzéshez használható egy harmadik féltől származó SIEM-eszközben Azure Monitor integrációval. További információ: az [Azure monitoring-adatok továbbítása az Event hub-hoz](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)

## <a name="send-to-log-analytics"></a>Küldés a Log Analyticsnek
Az Azure-beli erőforrásokhoz közvetlenül egy Log Analytics munkaterületre küldhetnek diagnosztikai adatokat, ahol az összetett lekérdezéseket a hibaelhárítás és az elemzés érdekében az információk alapján lehet futtatni.  További információ: az [Azure-erőforrás-naplók gyűjtése log Analytics munkaterületen Azure monitor](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace)

## <a name="limitations"></a>Korlátozások
Jelenleg a következő korlátozások vonatkoznak a metrikák exportálási funkciójának naplózására a terheléselosztó esetén:
* A metrikák jelenleg belső nevek használatával jelennek meg a naplókba való exportáláskor, a leképezést az alábbi táblázatban találja.
* A metrikák dimenzióját nem őrződnek meg. Például az olyan metrikákkal, mint a DipAvailability (állapot-mintavételi állapot), a háttérbeli IP-cím alapján nem lehet megosztani vagy megtekinteni
* A használt SNAT-portok és a lefoglalt SNAT-portok metrikái jelenleg nem érhetők el naplókként való exportáláshoz

## <a name="next-steps"></a>Következő lépések
* [Tekintse át a Load Balancer elérhető mérőszámait](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)
* [Lekérdezések létrehozása és tesztelése a Azure Monitor utasításokat követve](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)
* Küldjön visszajelzést erről a cikkről, vagy Load Balancer funkciót az alábbi hivatkozások használatával
