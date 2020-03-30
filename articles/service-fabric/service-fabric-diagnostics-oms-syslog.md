---
title: Linux-fürtesemények figyelése az Azure Service Fabricben
description: Ismerje meg, hogyan figyelheti a Service Fabric Linux-fürtesemények a Service Fabric platformesemények syslog írásával.
author: srrengar
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: srrengar
ms.openlocfilehash: 5bd3bda71943b2ba8a34cd4fbd0b20917b875670
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645752"
---
# <a name="service-fabric-linux-cluster-events-in-syslog"></a>Service Fabric Linux-fürtesemények a Syslogban

A Service Fabric platformesemények készletét teszi elérhetővé, hogy tájékoztassa a fürt fontos tevékenységéről. A kitett események teljes listája [itt](service-fabric-diagnostics-event-generation-operational.md)érhető el. Ezek az események számos módon használhatók fel. Ebben a cikkben fogjuk megvitatni, hogyan konfigurálhatja a Service Fabric ezeket az eseményeket a Syslog.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="introduction"></a>Bevezetés

A 6.4-es kiadásban a SyslogConsumer bevezetésre került, hogy a Service Fabric platformeseményeket a Syslog Linux-fürtöknek küldje el. Bekapcsolás után az események automatikusan a Syslog-ba kerülnek, amelyet a Log Analytics-ügynök gyűjthet és küldhet.

Minden Syslog esemény 4 összetevőből áll
* Létesítmény
* Identitás
* Üzenet
* Severity

A SyslogConsumer az összes platformeseményt `Local0`a Facility használatával írja. A config config módosításával bármely érvényes létesítményre frissíthet. A használt identitás `ServiceFabric`a . Az Üzenet mező tartalmazza a jsonban szerializált teljes eseményt, így számos eszköz lekérdezhető és felhasználható. 

## <a name="enable-syslogconsumer"></a>SyslogConsumer engedélyezése

A SyslogConsumer engedélyezéséhez a fürt frissítését kell végrehajtania. A `fabricSettings` szakaszt a következő kóddal kell frissíteni. Megjegyzés: ez a kód csak a SyslogConsumer-hez kapcsolódó szakaszokat tartalmazza

```json
    "fabricSettings": [
        {
            "name": "Diagnostics",
            "parameters": [
            {
                "name": "ConsumerInstances",
                "value": "AzureWinFabCsv, AzureWinFabCrashDump, AzureTableWinFabEtwQueryable, SyslogConsumer"
            }
            ]
        },
        {
            "name": "SyslogConsumer",
            "parameters": [
            {
                "name": "ProducerInstance",
                "value": "WinFabLttProducer"
            },
            {
            "name": "ConsumerType",
            "value": "SyslogConsumer"
            },
            {
                "name": "IsEnabled",
                "value": "true"
            }
            ]
        },
        {
            "name": "Common",
            "parameters": [
            {
                "name": "LinuxStructuredTracesEnabled",
                "value": "true"
            }
            ]
        }
    ],
```

Itt vannak a változások, hogy hívja fel
1. A Közös szakaszban van egy `LinuxStructuredTracesEnabled`új paraméter, a . **Erre a Syslog nak küldött Linux-események strukturált és szerializált rendszerezett.**
2. A Diagnosztika szakaszban egy új ConsumerInstance: SyslogConsumer lett hozzáadva. Ez azt mondja a platform van egy másik fogyasztó az eseményeket. 
3. Az új szakasz SyslogConsumer `IsEnabled` `true`kell, hogy a . Úgy van beállítva, hogy automatikusan használja a Local0 létesítményt. Ezt felülírhatja egy másik paraméter hozzáadásával.

```json
    {
        "name": "New LogFacility",
        "value": "<Valid Syslog Facility>"
    }
```

## <a name="azure-monitor-logs-integration"></a>Az Azure Monitor naplóinak integrációja
Ezeket a Syslog-eseményeket egy figyelési eszközben, például az Azure Monitor naplókban olvashatja. A Log Analytics-munkaterületet az Azure Marketplace használatával hozhatja létre az alábbi [utasítások] használatával. (.. /azure-monitor/learn/quick-create-workspace.md) A Log Analytics-ügynököt is hozzá kell adnia a fürthöz, hogy összegyűjtse és elküldje ezeket az adatokat a munkaterületre. Ez ugyanaz az ügynök, amelyet a teljesítményszámlálók gyűjtésére használnak. 

1. Keresse meg `Advanced Settings` a pengét

    ![Munkaterület beállításai](media/service-fabric-diagnostics-oms-syslog/workspace-settings.png)

2. Kattintson a következőre: `Data`
3. Kattintson a következőre: `Syslog`
4. Konfigurálja a Local0-t a nyomon követő eszközként. Hozzáadhat egy másik létesítményt, ha megváltoztatta a fabricBeállítások

    ![Syslog konfigurálása](media/service-fabric-diagnostics-oms-syslog/syslog-configure.png)
5. A lekérdezés megkezdéséhez `Logs` kattintson a lekérdezési kezelőre a munkaterületi erőforrás menüjében

    ![Munkaterületi naplók](media/service-fabric-diagnostics-oms-syslog/workspace-logs.png)
6. Lekérdezheti a `Syslog` `ServiceFabric` folyamatnévként keresett táblára. Az alábbi lekérdezés egy példa arra, hogyan elemezheti a JSON-t az eseményben, és hogyan jelenítheti meg annak tartalmát

```kusto
    Syslog | where ProcessName == "ServiceFabric" | extend $payload = parse_json(SyslogMessage) | project $payload
```

![Syslog lekérdezés](media/service-fabric-diagnostics-oms-syslog/syslog-query.png)

A fenti példa egy NodeDown-esemény. Az események teljes listáját [itt](service-fabric-diagnostics-event-generation-operational.md)tekintheti meg.

## <a name="next-steps"></a>További lépések
* [Telepítse a Log Analytics-ügynököt](service-fabric-diagnostics-oms-agent.md) a csomópontokra teljesítményszámlálók összegyűjtéséhez, valamint a docker-statisztikák és naplók gyűjtéséhez a tárolókhoz
* Ismerkedjen meg az Azure Monitor naplóinak részeként kínált [naplókeresési és lekérdezési](../log-analytics/log-analytics-log-searches.md) funkciókkal
* [Egyéni nézetek létrehozása az Azure Monitor naplóiban a Nézettervező használatával](../log-analytics/log-analytics-view-designer.md)
* Az Azure [Monitor naplóinak és a Syslog szolgáltatással való integrációjának hivatkozása.](../log-analytics/log-analytics-data-sources-syslog.md)
