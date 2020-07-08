---
title: A nyilvános alapszintű Load Balancerhoz tartozó műveletek, események és számlálók figyelése
titleSuffix: Azure Load Balancer
description: Megtudhatja, hogyan engedélyezheti a riasztási eseményeket és a nyilvános alapszintű Load Balancer a mintavétel állapotát.
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
ms.openlocfilehash: 1c8acd2d8055359288608f7bd2e5f54f8c4f6cc3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84809194"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>Az Azure Monitor naplózza a nyilvános Alapszintű Load Balancert

Az Azure-ban különböző típusú naplókat használhat az alapszintű Load Balancerek felügyeletéhez és hibakereséséhez. Ezek a naplók a portálon keresztül érhetők el. A naplók továbbíthatók egy Event hub-vagy Log Analytics-munkaterületre. Az összes napló kinyerhető az Azure Blob Storage-ból, és különböző eszközökön, például Excelben és Power BI is megtekinthető.  Az alábbi listából további információkat talál a naplók különböző típusairól.

* **Tevékenységek naplói:** A [megtekintési tevékenység naplói segítségével figyelheti az erőforrásokon végrehajtott műveleteket](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) , és megtekintheti az Azure-előfizetések és azok állapotát. A Tevékenységnaplók alapértelmezés szerint engedélyezve vannak, és a Azure Portal megtekinthetők.
* **Riasztási eseménynaplók:** Ez a napló a terheléselosztó által kiváltott riasztások megtekintésére használható. A terheléselosztó állapotát öt percenként gyűjti a rendszer. Ez a napló csak akkor íródik, ha a terheléselosztó riasztási eseménye bekövetkezik.
* **Állapot-mintavételi naplók:** Ezzel a naplóval megtekintheti az állapot-mintavétel által észlelt problémákat, például a háttérben található példányok számát, amelyek nem kapják meg a terheléselosztó kéréseit az állapot-mintavételi hibák miatt. Ez a napló akkor íródik le, amikor módosul az állapot-mintavételi állapot.

> [!IMPORTANT]
> Azure Monitor naplók jelenleg csak nyilvános alapszintű terheléselosztó esetén működnek. A naplók csak a Resource Manager-alapú üzemi modellben üzembe helyezett erőforrások esetében érhetők el. A klasszikus üzemi modell erőforrásaihoz nem használhat naplókat. További információ az üzembe helyezési modellekről: a [Resource Manager telepítésének ismertetése és a klasszikus üzembe helyezés](../azure-resource-manager/management/deployment-models.md).

## <a name="enable-logging"></a>Naplózás engedélyezése

A tevékenységnaplózás automatikusan engedélyezve van minden Resource Manager-erőforráshoz. Az esemény-és állapot-mintavételi naplózás engedélyezése a naplókon keresztül elérhető adatok gyűjtésének megkezdéséhez. A naplózás engedélyezéséhez kövesse az alábbi lépéseket.

Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Ha még nem rendelkezik terheléselosztó, a folytatás előtt [hozzon létre egy Load balancert](https://docs.microsoft.com/azure/load-balancer/quickstart-create-basic-load-balancer-portal) .

1. A portálon kattintson az **erőforráscsoportok**elemre.
2. Válassza ki **\<resource-group-name>** a terheléselosztó helyét.
3. Válassza ki a Load balancert.
4. Válassza a **figyelés**  >  **diagnosztikai beállítások**lehetőséget.
5. A **diagnosztika beállításai** ablaktábla **diagnosztika beállításai**területén válassza a **+ diagnosztikai beállítás hozzáadása**elemet.
6. A **diagnosztikai beállítások** létrehozása panelen adja meg a **MyLBDiagnostics** nevet a **név** mezőben.
7. A **diagnosztikai beállításoknak**három lehetősége van.  A követelmények közül választhat egyet, kettőt vagy mindháromat, és konfigurálhatja a következőt:
   * **Archiválás egy Storage-fiókba**
   * **Stream az Event hub-ba**
   * **Küldés a Log Analyticsnek**

    ### <a name="archive-to-a-storage-account"></a>Archiválás tárfiókba
    Ehhez a folyamathoz már létre kell hoznia egy Storage-fiókot.  A Storage-fiók létrehozásával kapcsolatban tekintse meg [a Storage-fiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) című témakört.

    1. Jelölje be az archiválás elem melletti jelölőnégyzetet a **Storage-fiókhoz**.
    2. Válassza a **Konfigurálás** lehetőséget a **Storage-fiók kiválasztása** panel megnyitásához.
    3. Válassza ki azt az **előfizetést** , amelyben a Storage-fiókot létrehozták a legördülő listából.
    4. A legördülő listában válassza ki a Storage-fiók nevét a **Storage-fiók** területen.
    5. Kattintson az OK gombra.

    ### <a name="stream-to-an-event-hub"></a>Streamelés eseményközpontba
    Ehhez a folyamathoz már létrehozott Event hub szükséges.  Az Event hub létrehozásával kapcsolatban tekintse meg az [Event hub létrehozása Azure Portal használatával](https://docs.microsoft.com/azure/event-hubs/event-hubs-create) című témakört.

    1. Jelölje be a stream elem melletti jelölőnégyzetet az **Event hubhoz**
    2. A **Konfigurálás** elemre kattintva nyissa meg az **Event hub kiválasztása** ablaktáblát.
    3. Válassza ki azt az **előfizetést** , amelyben az Event hub létre lett hozva a legördülő listából.
    4. **Válassza ki az Event hub-névteret** a legördülő listából.
    5. **Válassza az Event hub-házirend neve elemet** a legördülő listából.
    6. Kattintson az OK gombra.

    ### <a name="send-to-log-analytics"></a>Küldés a Log Analyticsnek
    Ehhez a folyamathoz már létre kell hoznia és konfigurálnia kell egy log Analytics-munkaterületet.  Log Analytics munkaterület létrehozásával kapcsolatban lásd: [log Analytics munkaterület létrehozása a Azure Portal](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

    1. Jelölje be a **küldés log Analyticsra**jelölőnégyzetet.
    2. Válassza ki azt az **előfizetést** , ahol a log Analytics munkaterület a legördülő mezőben található.
    3. Válassza ki a **log Analytics munkaterületet** a legördülő listából.


8. A **diagnosztika beállításai** ablaktábla **napló** szakasza alatt jelölje be a mindkettő melletti jelölőnégyzetet:
   * **LoadBalancerAlertEvent**
   * **LoadBalancerProbeHealthStatus**

9.  A **diagnosztika beállításai** ablaktábla **metrika** szakasza alatt jelölje be a jelölőnégyzetet a következő mellett:
   * **AllMetrics**

11. Győződjön meg arról, hogy minden helyes, és kattintson a **Mentés** gombra a **diagnosztikai beállítások** létrehozása ablaktábla tetején.

## <a name="activity-log"></a>Tevékenységnapló

A rendszer alapértelmezés szerint létrehozza a tevékenység naplóját. A naplók 90 napig őrződnek meg az Azure Eseménynapló-tárolójában. További információ ezekről a naplókról: a [tevékenység naplóinak megtekintése az erőforrásokkal kapcsolatos műveletek figyelése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) című cikkben.

## <a name="archive-to-storage-account-logs"></a>Archiválás a Storage-fiókok naplóiba

### <a name="alert-event-log"></a>Riasztási Eseménynapló

Ez a napló csak akkor jön létre, ha a terheléselosztó alapján engedélyezte. Az események JSON formátumban vannak naplózva, és a naplózás engedélyezésekor megadott Storage-fiókban tárolódnak. Az alábbi példa egy eseményt mutat be.

```json
{
    "time": "2016-01-26T10:37:46.6024215Z",
    "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }
}
```

A JSON-kimenet a *EventName* tulajdonságot jeleníti meg, amely leírja, hogy a terheléselosztó miért hozta létre a riasztást. Ebben az esetben a generált riasztás a forrás IP NAT-korlátok (SNAT) által okozott TCP-portok kimerülése miatt lett létrehozva.

### <a name="health-probe-log"></a>Állapot mintavételi naplója

Ez a napló csak akkor jön létre, ha a fentiekben részletezett módon engedélyezte a terheléselosztást. Az adatai a naplózás engedélyezésekor megadott Storage-fiókban tárolódnak. Létrejön egy "elemzések – naplók-loadbalancerprobehealthstatus" nevű tároló, amely a következő adatnaplókat rögzíti:

```json
{
    "records":[
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 1,
            "healthPercentage": 50.000000
        }
    },
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 0,
            "healthPercentage": 100.000000
        }
    }]
}
```

A JSON-kimenet a tulajdonságok mezőben a mintavételi állapot állapotának alapvető információit jeleníti meg. A *dipDownCount* tulajdonság a háttérbeli példányok teljes számát jeleníti meg, amelyek a sikertelen mintavételi válaszok miatt nem kapják meg a hálózati forgalmat.

### <a name="view-and-analyze-the-activity-log"></a>A tevékenységnapló megtekintése és elemzése

A következő módszerek bármelyikével megtekintheti és elemezheti a tevékenység-naplózási adataikat:

* **Azure-eszközök:** A Azure PowerShellon, az Azure parancssori felületen (CLI), az Azure REST API vagy a Azure Portalon keresztül kérhet le adatokat a tevékenység naplóból. Az egyes módszerek részletes utasításait a [Resource Managerrel kapcsolatos naplózási műveletek](../azure-resource-manager/management/view-activity-logs.md) című cikkben találja.
* **Power bi:** Ha még nem rendelkezik [Power bi](https:// .microsoft.com/pricing) -fiókkal, ingyenes kipróbáláshoz is kipróbálhatja. Az [Azure-naplók a Power BIhez készült tartalomkezelő csomag](https:// .microsoft.com/documentation/ -content-pack-azure-audit-logs)használatával az adatokat előre konfigurált irányítópultokkal elemezheti, vagy testre szabhatja a nézeteket, hogy megfeleljenek a követelményeinek.

### <a name="view-and-analyze-the-health-probe-and-event-log"></a>Az állapot-mintavétel és az Eseménynapló megtekintése és elemzése

Kapcsolódjon a Storage-fiókhoz, és kérje le az Event és Health mintavételi naplók JSON-naplófájljait. A JSON-fájlok letöltése után konvertálhatja őket CSV formátumba, és megtekintheti az Excelben, Power BI vagy bármely más adatvizualizációs eszközön.

> [!TIP]
> Ha ismeri a Visual Studiót, illetve C#-állandók és -változók módosításának alapfogalmait, használja a GitHubról elérhető [naplókonvertáló eszközöket](https://github.com/Azure-Samples/networking-dotnet-log-converter).

## <a name="stream-to-an-event-hub"></a>Streamelés eseményközpontba
Ha a diagnosztikai adatokat egy esemény központba továbbítják, akkor a központi naplózási elemzéshez használható egy harmadik féltől származó SIEM-eszközben Azure Monitor integrációval. További információ: az [Azure monitoring-adatok továbbítása az Event hub-hoz](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)

## <a name="send-to-log-analytics"></a>Küldés a Log Analyticsnek
Az Azure-beli erőforrásokhoz közvetlenül egy Log Analytics munkaterületre küldhetnek diagnosztikai adatokat, ahol az összetett lekérdezéseket a hibaelhárítás és az elemzés érdekében az információk alapján lehet futtatni.  További információ: az [Azure-erőforrás-naplók gyűjtése log Analytics munkaterületen Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace)

## <a name="next-steps"></a>További lépések

[A Load Balancer vizsgálatok ismertetése](load-balancer-custom-probe-overview.md)
