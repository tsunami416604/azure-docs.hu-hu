---
title: Az Azure Service Fabric Linux-fürt események monitorozása |} A Microsoft Docs
description: Linux rendszerű fürt eseményeket a Syslog monitorozása
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/23/2018
ms.author: srrengar
ms.openlocfilehash: 5989ec9f95e2f5129a128d19326833718f37b508
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52730231"
---
# <a name="service-fabric-linux-cluster-events-in-syslog"></a>A Syslog Linux-alapú Service Fabric-fürt események

A Service Fabric platform eseményeket tájékoztatják a fürtben található fontos tevékenység tesz elérhetővé. Érhető el, amelyek ki vannak téve események teljes listáját [Itt](service-fabric-diagnostics-event-generation-operational.md). Nincsenek többféle módon, amelyen keresztül az események használhatók fel. Ebben a cikkben fogjuk megvitatni a Service Fabric írni ezeket az eseményeket a Syslog konfigurálása.

## <a name="introduction"></a>Bevezetés

A 6.4-es kiadásban a SyslogConsumer fejlődéséből küldhet a Service Fabric platform eseményeket Syslog Linux-fürtök esetén. Ha bekapcsolta, események automatikusan átkerülnek a Log Analytics-ügynök által küldött és gyűjtött Syslog.

Minden Syslog esemény 4 részből áll
* Létesítmény
* Identitás
* Üzenet
* Severity

A SyslogConsumer szolgáltatást használó összes platform eseményt ír `Local0`. Bármely érvényes létesítmény is lehet frissíteni a konfigurációs config módosításával. Az identitás használt `ServicFabric`. Az üzenet mezőbe a JSON-szerializált, így, lekérdezhetők és eszközök széles által felhasznált teljes eseményt tartalmaz. 

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
2. A diagnosztika szakaszban egy új ConsumerInstance: SyslogConsumer hozzá lett adva. Ez jelzi a platform az eseményeket az egy másik felhasználója van. 
3. Az új szakasz SyslogConsumer rendelkeznie kell `IsEnabled` , `true`. A Local0 létesítmény automatikusan használandó profilhoz. Ez felülírható egy másik paraméter hozzáadásával.

```json
    {
        "name": "New LogFacility",
        "value": "<Valid Syslog Facility>"
    }
```

## <a name="log-analytics-integration"></a>Log Analytics-integráció
Ezeket a figyelési eszközben, például a Log Analytics Syslog-események olvashatja. Létrehozhat egy Log Analytics-munkaterületet az Azure piactéren, használatával ezen [útmutató] használatával. (.. / log-analytics/log-analytics-quick-create-workspace.md) is hozzá kell a Log Analytics-ügynök gyűjtenek, majd elküldi ezeket az adatokat a munkaterületet a fürthöz. Ez a teljesítményszámlálók adatainak összegyűjtése használt ugyanaz az ügynök. 

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
* Ismerkedjen meg a [naplókeresési és lekérdezési](../log-analytics/log-analytics-log-searches.md) funkciók a Log Analytics részeként érhető el
* [Nézettervező segítségével egyéni nézeteket hozhat létre a Log Analyticsben](../log-analytics/log-analytics-view-designer.md)
* Hogyan referenciája [Syslog Log Analytics-integráció](../log-analytics/log-analytics-data-sources-syslog.md).
