---
title: Nyilvános alapterhelés-elosztó műveleteinek, eseményeinek és számlálóinak figyelése
titleSuffix: Azure Load Balancer
description: További információ a riasztási események engedélyezéséről és a nyilvános alapszintű terheléselosztó állapotnaplózásának vizsgálatáról
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: allensu
ms.openlocfilehash: 0a21af683d9fa7849d3e96c545983c9f40a8d4c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76935318"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>Az Azure Monitor naplózza a nyilvános Alapszintű Load Balancert

>[!IMPORTANT]
>Az Azure Load Balancer két különböző típust támogat: Alapszintű és Standard. Ez a cikk az Alapszintű Load Balancert ismerteti. A standard terheléselosztóról további információt a [Standard load Balancer áttekintése,](load-balancer-standard-overview.md) amely telemetriai adatokat az Azure Monitor többdimenziós metrikákon keresztül elérhetővé teszi.

Az Azure-ban különböző típusú naplókat használhat az alapszintű terheléselosztók kezeléséhez és hibaelhárításához. A naplók némelyike a portálon keresztül érhető el. A naplók streamelhetők egy eseményközpontba vagy egy Log Analytics-munkaterületre. Az összes napló kinyerhető az Azure blob storage-ból, és különböző eszközökben, például az Excelben és a Power BI-ban tekinthető meg.  Az alábbi listából többet is megtudhat a különböző típusú naplókról.

* **Tevékenységnaplók:** A tevékenységnaplók megtekintése segítségével [figyelheti az erőforrásokon végrehajtott műveleteket](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) az Azure-előfizetés(ek)beküldött összes tevékenység és azok állapotának megtekintéséhez. A tevékenységnaplók alapértelmezés szerint engedélyezve vannak, és megtekinthetők az Azure Portalon.
* **Riasztási eseménynaplók:** Ezzel a naplóval megtekintheti a terheléselosztó által emelt riasztásokat. A terheléselosztó állapotát öt percenként gyűjti a nap. Ez a napló csak akkor íródik, ha egy terheléselosztó riasztási esemény van előírva.
* **Állapot-mintavételi naplók:** Ezzel a naplóval megtekintheti az állapotminta által észlelt problémákat, például a háttérkészletben lévő olyan példányok számát, amelyek nem fogadnak kéréseket a terheléselosztótól az állapotminta hibái miatt. Ez a napló akkor van írva, ha az állapotminta állapota megváltozik.

> [!IMPORTANT]
> Az Azure Monitor naplói jelenleg csak a nyilvános alapszintű terheléselosztók. A naplók csak az Erőforrás-kezelő telepítési modelljében üzembe helyezett erőforrásokhoz érhetők el. A klasszikus központi telepítési modellben lévő erőforrásokhoz nem használhat naplókat. A központi telepítési modellekről az [Erőforrás-kezelő telepítésének és a klasszikus telepítésnek a ismertetése című témakörben](../azure-resource-manager/management/deployment-models.md)talál további információt.

## <a name="enable-logging"></a>Naplózás engedélyezése

A tevékenységnaplózás automatikusan engedélyezve van minden Resource Manager-erőforráshoz. Engedélyezze az esemény- és állapotminta-naplózást az ezeken a naplókon keresztül elérhető adatok gyűjtésének megkezdéséhez. A naplózás engedélyezéséhez kövesse az alábbi lépéseket.

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Ha még nem rendelkezik terheléselosztóval, [hozzon létre egy terheléselosztót](https://docs.microsoft.com/azure/load-balancer/quickstart-create-basic-load-balancer-portal) a folytatás előtt.

1. A portálon kattintson az **Erőforráscsoportok**elemre.
2. Válassza ki ** \<az erőforráscsoport-név>,** ahol a terheléselosztó van.
3. Válassza ki a terheléselosztót.
4. Válassza a Diagnosztikai beállítások **figyelése** > **lehetőséget.**
5. A **Diagnosztika beállításai** ablaktábla **Diagnosztika beállításai területén**válassza a + Add Diagnostic setting **(Diagnosztikai beállítás hozzáadása) lehetőséget.**
6. A **Diagnosztikai beállítások** létrehozása ablaktáblán írja be a **myLBDiagnostics** értéket a **Név** mezőbe.
7. A **Diagnosztikai beállítások**három lehetősége van.  Kiválaszthatja az egyik, két vagy mindhárom, és konfigurálja az egyes követelményeknek:
   * **Archiválás tárfiókba**
   * **Streamelés eseményközpontba**
   * **Küldés a Log Analyticsnek**

    ### <a name="archive-to-a-storage-account"></a>Archiválás tárfiókba
    Ehhez a folyamathoz már létrehozott tárfiókra lesz szüksége.  Tárfiók létrehozásához lásd: [Tárfiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)

    1. Jelölje be az **Archiválás tárfiókhoz jelölőnégyzetet.**
    2. Válassza a **Konfigurálás** lehetőséget a **Tárfiók kiválasztása** ablaktábla megnyitásához.
    3. Válassza ki azt az **előfizetést,** ahol a tárfiókot létrehozták a legördülő mezőben.
    4. Válassza ki a tárfiók nevét a Legördülő lista **Tárfiók** mezőjében.
    5. Kattintson az OK gombra.

    ### <a name="stream-to-an-event-hub"></a>Streamelés eseményközpontba
    Ehhez a folyamathoz már létrehozott eseményközpontra lesz szükség.  Eseményközpont létrehozásához olvassa el [a rövid útmutató: Eseményközpont létrehozása az Azure Portal használatával című témakört.](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)

    1. Jelölje be az **Eseményközpontba való adatfolyam** mező jelölőnégyzetet
    2. Válassza a **Konfigurálás** lehetőséget az **Eseményközpont kiválasztása** ablaktábla megnyitásához.
    3. Válassza ki azt az **Előfizetést,** ahol az eseményközpontot létrehozták a legördülő mezőben.
    4. **Válassza ki az eseményközpont névterét** a legördülő mezőben.
    5. **Válassza ki az eseményközpont házirendjének nevét** a legördülő mezőben.
    6. Kattintson az OK gombra.

    ### <a name="send-to-log-analytics"></a>Küldés a Log Analyticsnek
    Ehhez a folyamathoz már létre kell hoznia és konfigurálnia kell egy naplóelemzési munkaterületet.  Log Analytics-munkaterület létrehozásáról a [Log Analytics-munkaterület létrehozása az Azure Portalon](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) című témakörben

    1. Jelölje be a **Küldés**a Log Analytics szolgáltatásba jelölőnégyzetet.
    2. Válassza ki azt az **előfizetést,** ahol a Log Analytics-munkaterület a legördülő mezőben található.
    3. Válassza ki a **Log Analytics-munkaterületet** a legördülő mezőben.


8. A **Diagnosztikai beállítások** ablaktábla **LOG** szakasza alatt jelölje be a mindkettő melletti jelölőnégyzetet:
   * **LoadBalancerAlertEvent**
   * **LoadBalancerProbeHealthStatus**

9.  A **Diagnosztika beállításai** ablaktábla **MetMETRIA** szakasza alatt jelölje be a következő jelölőnégyzetet:
   * **AllMetrics**

11. Ellenőrizze, hogy minden **Save** helyesnek tűnik-e, és kattintson a Mentés gombra a **Diagnosztikai beállítások** létrehozása ablaktábla tetején.

## <a name="activity-log"></a>Tevékenységnapló

A tevékenységnapló alapértelmezés szerint jön létre. A naplók 90 napig megmaradnak az Azure Eseménynaplók tárolójában. Ezekről a naplókról a Tevékenységnaplók megtekintése című cikkben a Tevékenységnaplók megtekintése című cikk ben olvashat [bővebben.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)

## <a name="archive-to-storage-account-logs"></a>Archiválás tárfiók-naplókba

### <a name="alert-event-log"></a>Eseménynapló riasztása

Ez a napló csak akkor jön létre, ha terheléselosztónként engedélyezte. Az események JSON formátumban vannak naplózva, és a naplózás engedélyezésekor megadott tárfiókban tárolódnak. A következő példa egy eseményre mutat.

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

A JSON kimenet az *eventname tulajdonságot* jeleníti meg, amely leírja a riasztást létrehozó terheléselosztó okát. Ebben az esetben a létrehozott riasztás a Forrás IP NAT-határértékek (SNAT) által okozott TCP-port kimerülése miatt jött létre.

### <a name="health-probe-log"></a>Állapot-mintavételi napló

Ez a napló csak akkor jön létre, ha a fent részletezett terheléselosztási alapon engedélyezte. Az adatok a naplózás engedélyezésekor megadott tárfiókban tárolódnak. Létrejön egy "insights-logs-loadbalancerprobehealthstatus" nevű tároló, amely a következő adatokat naplózza:

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

A JSON kimenet a tulajdonságok mezőben mutatja a mintavétel állapotának alapvető adatait. A *dipDownCount* tulajdonság a háttér-háttér-példányok teljes számát jeleníti meg, amelyek sikertelen mintavételi válaszok miatt nem kapnak hálózati forgalmat.

### <a name="view-and-analyze-the-activity-log"></a>A tevékenységnapló megtekintése és elemzése

A tevékenységnapló adatait az alábbi módszerek bármelyikével tekintheti meg és elemezheti:

* **Azure-eszközök:** Adatok lekérése a tevékenységnaplóból az Azure PowerShellen, az Azure command line interface (CLI), az Azure REST API-n vagy az Azure Portalon keresztül. Az egyes módszerek részletes útmutatóját az [Erőforrás-kezelő naplózási műveleteiről](../azure-resource-manager/management/view-activity-logs.md) szóló cikk ismerteti.
* **Power BI:** Ha még nem rendelkezik [Power BI-fiókkal,](https:// .microsoft.com/pricing) kipróbálhatja ingyen. Az [Azure Naplózási naplók tartalomcsomag](https:// .microsoft.com/documentation/ -content-pack-azure-audit-logs)használatával elemezheti az adatokat előre konfigurált irányítópultokkal, vagy testreszabhatja a nézeteket az igényeinek megfelelően.

### <a name="view-and-analyze-the-health-probe-and-event-log"></a>Az állapotminta és az eseménynapló megtekintése és elemzése

Csatlakozzon a tárfiókhoz, és kérje le a JSON-naplóbejegyzéseket az esemény- és állapotmintanaplókhoz. A JSON-fájlok letöltése után csv-vé konvertálhatja őket, és megtekintheti őket az Excelben, a Power BI-ban vagy bármely más adatvizualizációs eszközben.

> [!TIP]
> Ha ismeri a Visual Studiót, illetve C#-állandók és -változók módosításának alapfogalmait, használja a GitHubról elérhető [naplókonvertáló eszközöket](https://github.com/Azure-Samples/networking-dotnet-log-converter).

## <a name="stream-to-an-event-hub"></a>Streamelés eseményközpontba
Diagnosztikai adatok streamelése egy eseményközpontba, akkor használható központosított naplóelemzés egy külső SIEM-eszköz az Azure Monitor-integráció. További információ: [Stream Azure figyelési adatok egy eseményközpontba](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)

## <a name="send-to-log-analytics"></a>Küldés a Log Analyticsnek
Az Azure-beli erőforrások diagnosztikai adatait közvetlenül a Log Analytics-munkaterületre küldhetik, ahol összetett lekérdezések futtathatók a hibaelhárításés és elemzéshez szükséges információkon.  További információ: [Azure-erőforrásnaplók gyűjtése a Log Analytics-munkaterületen az Azure Monitorban](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace)

## <a name="next-steps"></a>További lépések

[A Load Balancer vizsgálatok ismertetése](load-balancer-custom-probe-overview.md)
