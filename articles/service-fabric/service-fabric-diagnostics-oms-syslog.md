---
title: Az Azure Service Fabric Linux-fürt események monitorozása |} A Microsoft Docs
description: Linux rendszerű fürt eseményeket a Syslog monitorozása
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/23/2018
ms.author: srrengar
ms.openlocfilehash: 402e3dfe018c94ef068caf918b38aaad00064a49
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670370"
---
# <a name="service-fabric-linux-cluster-events-in-syslog"></a>A Syslog Linux-alapú Service Fabric-fürt események

A Service Fabric platform eseményeket tájékoztatják a fürtben található fontos tevékenység tesz elérhetővé. Érhető el, amelyek ki vannak téve események teljes listáját [Itt](service-fabric-diagnostics-event-generation-operational.md). Nincsenek többféle módon, amelyen keresztül az események használhatók fel. Ebben a cikkben fogjuk megvitatni a Service Fabric írni ezeket az eseményeket a Syslog konfigurálása.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="introduction"></a>Bevezetés

A 6.4-es kiadásban a SyslogConsumer fejlődéséből küldhet a Service Fabric platform eseményeket Syslog Linux-fürtök esetén. Ha bekapcsolta, események automatikusan átkerülnek a Log Analytics-ügynök által küldött és gyűjtött Syslog.

Minden Syslog esemény 4 részből áll
* Szolgáltatás
* Identitás
* Üzenet
* Severity

A SyslogConsumer szolgáltatást használó összes platform eseményt ír `Local0`. Bármely érvényes létesítmény is lehet frissíteni a konfigurációs config módosításával. Az identitás használt `ServiceFabric`. Az üzenet mezőbe a JSON-szerializált, így, lekérdezhetők és eszközök széles által felhasznált teljes eseményt tartalmaz. 

## <a name="enable-syslogconsumer"></a>SyslogConsumer engedélyezése

Ahhoz, hogy a SyslogConsumer, végezze el a fürt frissítését kell. A `fabricSettings` szakaszban a következő kóddal frissíteni kell. Megjegyzés: Ez a kód csak tartalmaz SyslogConsumer kapcsolódó szakaszok

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

Itt láthatók az hívásához
1. A közös szakaszban van egy új paraméter `LinuxStructuredTracesEnabled`. **Ez azért szükséges, a Linux-események strukturált és szerializálni a Syslog történő küldésekor.**
2. A diagnosztika szakaszban egy új ConsumerInstance: SyslogConsumer hozzá van adva. Ez jelzi a platform az eseményeket az egy másik felhasználója van. 
3. Az új szakasz SyslogConsumer rendelkeznie kell `IsEnabled` , `true`. A Local0 létesítmény automatikusan használatára van konfigurálva. Ez felülírható egy másik paraméter hozzáadásával.

```json
    {
        "name": "New LogFacility",
        "value": "<Valid Syslog Facility>"
    }
```

## <a name="azure-monitor-logs-integration"></a>Az Azure Monitor-naplók integrációja
Ezeket a figyelési eszközben, például az Azure Monitor naplóira Syslog-események olvashatja. Létrehozhat egy Log Analytics-munkaterületet az Azure piactéren, használatával ezen [útmutató] használatával. (.. / azure-monitor/learn/quick-create-workspace.md) is hozzá kell a Log Analytics-ügynök gyűjtenek, majd elküldi ezeket az adatokat a munkaterületet a fürthöz. Ez a teljesítményszámlálók adatainak összegyűjtése használt ugyanaz az ügynök. 

1. Keresse meg a `Advanced Settings` panel

    ![Munkaterület beállításai](media/service-fabric-diagnostics-oms-syslog/workspace-settings.png)

2. Kattintson a következőre: `Data`
3. Kattintson a következőre: `Syslog`
4. Local0 konfigurálja a létesítmény nyomon követéséhez. Ha a fabricSettings adhat hozzá egy másik létesítményben

    ![Syslog konfigurálása](media/service-fabric-diagnostics-oms-syslog/syslog-configure.png)
5. Látogasson el a lekérdezéskezelő kattintva `Logs` lekérdezéséről a munkaterület-erőforráshoz menü

    ![Munkaterület-naplók](media/service-fabric-diagnostics-oms-syslog/workspace-logs.png)
6. Végezhet lekérdezéseket a `Syslog` táblában keres `ServiceFabric` a ProcessName szerint. Az alábbi lekérdezés a példa bemutatja, hogyan elemezheti a JSON-t az esemény, és a tartalom megjelenítéséhez

```kusto
    Syslog | where ProcessName == "ServiceFabric" | extend $payload = parse_json(SyslogMessage) | project $payload
```

![Syslog-lekérdezés](media/service-fabric-diagnostics-oms-syslog/syslog-query.png)

A fenti példa NodeDown esemény van. Az események teljes listáját megtekintheti [Itt](service-fabric-diagnostics-event-generation-operational.md).

## <a name="next-steps"></a>További lépések
* [A Log Analytics-ügynök telepítésének](service-fabric-diagnostics-oms-agent.md) alakzatot a csomópontok összegyűjtéséhez a teljesítményszámlálókat és a docker-statisztikák és a tárolókhoz naplóinak összegyűjtésére
* Ismerkedjen meg a [naplókeresési és lekérdezési](../log-analytics/log-analytics-log-searches.md) szolgáltatásai által kínált Azure Monitor naplóira
* [Nézettervező segítségével egyéni nézeteket hozhat létre az Azure Monitor naplóira](../log-analytics/log-analytics-view-designer.md)
* Hogyan referenciája [Azure Monitor integrációja naplózza a Syslog](../log-analytics/log-analytics-data-sources-syslog.md).
