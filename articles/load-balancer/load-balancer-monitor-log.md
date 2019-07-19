---
title: A nyilvános alapszintű Load Balancerhoz tartozó műveletek, események és számlálók figyelése
titlesuffix: Azure Load Balancer
description: Megtudhatja, hogyan engedélyezheti a riasztási eseményeket és a nyilvános alapszintű Load Balancer a mintavétel állapotát.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/10/2018
ms.author: allensu
ms.openlocfilehash: 1995ad5e8179fdee11e960c2ad0e7c03602ebd31
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274806"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>Nyilvános alapszintű Load Balancer naplók Azure Monitor

>[!IMPORTANT] 
>Azure Load Balancer két különböző típust támogat: alap- és standard szinten. Ez a cikk az Alapszintű Load Balancert ismerteti. További információ a standard Load Balancerről: [standard Load Balancer áttekintése](load-balancer-standard-overview.md) , amely a Azure monitor többdimenziós metrikák használatával teszi elérhetővé a telemetria.

Az Azure-ban különböző típusú naplókat használhat az alapszintű Load Balancerek felügyeletéhez és hibakereséséhez. Ezek a naplók a portálon keresztül érhetők el. Az összes napló kinyerhető az Azure Blob Storage-ból, és különböző eszközökön, például az Excelben és a PowerBI is megtekinthető. Az alábbi listából további információkat talál a naplók különböző típusairól.

* **Naplók:** Az Azure- [naplók](../monitoring-and-diagnostics/insights-debugging-with-events.md) (korábbi nevén operatív naplók) használatával megtekintheti az Azure-előfizetése (i) ra küldött összes műveletet, valamint azok állapotát. A naplók alapértelmezés szerint engedélyezve vannak, és a Azure Portal is megtekinthetők.
* **Riasztási eseménynaplók:** Ez a napló a terheléselosztó által kiváltott riasztások megtekintésére használható. A terheléselosztó állapotát öt percenként gyűjti a rendszer. Ez a napló csak akkor íródik, ha a terheléselosztó riasztási eseménye bekövetkezik.
* **Állapot-mintavételi naplók:** Ezzel a naplóval megtekintheti az állapot-mintavétel által észlelt problémákat, például a háttérben található példányok számát, amelyek nem kapják meg a terheléselosztó kéréseit az állapot-mintavételi hibák miatt. Ez a napló akkor íródik le, amikor módosul az állapot-mintavételi állapot.

> [!IMPORTANT]
> Azure Monitor naplók jelenleg csak nyilvános alapszintű terheléselosztó esetén működnek. A naplók csak a Resource Manager-alapú üzemi modellben üzembe helyezett erőforrások esetében érhetők el. A klasszikus üzemi modell erőforrásaihoz nem használhat naplókat. További információ az üzembe helyezési modellekről: a [Resource Manager telepítésének ismertetése és a klasszikus üzembe helyezés](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="enable-logging"></a>Naplózás engedélyezése

A naplózás minden erőforrás-kezelő erőforrás esetében automatikusan engedélyezve van. A naplókon keresztül elérhető adatok gyűjtésének megkezdéséhez engedélyeznie kell az esemény-és állapot-mintavételi naplózást. A naplózás engedélyezéséhez kövesse az alábbi lépéseket.

Jelentkezzen be a [Azure Portalba](https://portal.azure.com). Ha még nem rendelkezik terheléselosztó, a folytatás előtt [hozzon létre egy Load balancert](load-balancer-get-started-internet-arm-ps.md) .

1. A portálon kattintson a **Tallózás**gombra.
2. Válassza a **terheléselosztó**lehetőséget.

    ![portál – terheléselosztó](./media/load-balancer-monitor-log/load-balancer-browse.png)

3. Válasszon ki egy meglévő terheléselosztó > > **az összes beállítást**.
4. A Load Balancer neve alatt, a párbeszédpanel jobb oldalán görgessen a **figyelés**elemre, és kattintson a **diagnosztika**elemre.

    ![portál – Load-Balancer – beállítások](./media/load-balancer-monitor-log/load-balancer-settings.png)

5. A **diagnosztika** ablaktábla **állapot**területén válassza **a be**lehetőséget.
6. Kattintson a **Storage-fiók**elemre.
7. A **naplók**területen válasszon ki egy meglévő Storage-fiókot, vagy hozzon létre egy újat. A csúszka használatával meghatározhatja, hogy az események hány napja lesz tárolva az eseménynaplókban. 
8. Kattintson a **Save** (Mentés) gombra.

A diagnosztika a megadott Storage-fiókban Table Storage lesz mentve. Ha a rendszer nem menti a naplókat, az azért van, mert nem készülnek hozzá kapcsolódó naplók.

![Portál – diagnosztikai naplók](./media/load-balancer-monitor-log/load-balancer-diagnostics.png)

> [!NOTE]
> A naplókban nem szükséges külön Storage-fiók. Az esemény-és állapot-mintavételek naplózása szolgáltatás használati díjat számít fel.

## <a name="audit-log"></a>Napló

A rendszer alapértelmezés szerint a naplót hozza létre. A naplók 90 napig őrződnek meg az Azure Eseménynapló-tárolójában. A naplókról további információt az [események megtekintése és a naplók naplózása](../monitoring-and-diagnostics/insights-debugging-with-events.md) című cikkben talál.

## <a name="alert-event-log"></a>Riasztási Eseménynapló

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

A JSON-kimenet megjeleníti a *EventName* tulajdonságot, amely leírja, hogy a terheléselosztó miért hozta létre a riasztást. Ebben az esetben a generált riasztást a forrás IP NAT-korlátok (SNAT) által okozott TCP-port kimerülése okozta.

## <a name="health-probe-log"></a>Állapot mintavételi naplója

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

## <a name="view-and-analyze-the-audit-log"></a>A napló megtekintése és elemzése

A naplózási napló adatai a következő módszerek bármelyikével megtekinthetők és elemezhetők:

* **Azure-eszközök:** Információk beolvasása a naplókból a Azure PowerShell, az Azure parancssori felület (CLI), az Azure REST API vagy az Azure betekintő portálon keresztül. Az egyes módszerek részletes utasításait a [Resource Managerrel kapcsolatos naplózási műveletek](../azure-resource-manager/resource-group-audit.md) című cikkben találja.
* **Power BI:** Ha még nem rendelkezik [Power bi](https://powerbi.microsoft.com/pricing) -fiókkal, kipróbálhatja ingyenesen. Az Azure-naplók a Power BIhez készült [tartalomkezelő csomag](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs)használatával az adatokat előre konfigurált irányítópultokkal elemezheti, vagy testre szabhatja a nézeteket, hogy megfeleljenek a követelményeinek.

## <a name="view-and-analyze-the-health-probe-and-event-log"></a>Az állapot-mintavétel és az Eseménynapló megtekintése és elemzése

Csatlakoznia kell a Storage-fiókjához, és be kell olvasnia az esemény-és állapot-mintavételi naplók JSON-naplófájljait. A JSON-fájlok letöltése után konvertálhatja őket CSV-re, és megtekintheti őket az Excelben, a PowerBI vagy bármely más adatvizualizációs eszközön.

> [!TIP]
> Ha ismeri a Visual Studiót, illetve C#-állandók és -változók módosításának alapfogalmait, használja a GitHubról elérhető [naplókonvertáló eszközöket](https://github.com/Azure-Samples/networking-dotnet-log-converter).

## <a name="additional-resources"></a>További források

* Az [Azure-naplók megjelenítését Power bi](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) blogbejegyzésben teheti meg.
* [Megtekintheti és elemezheti az Azure-naplókat Power bi és további](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogbejegyzésekben.

## <a name="next-steps"></a>További lépések

[A Load Balancer vizsgálatok ismertetése](load-balancer-custom-probe-overview.md)
