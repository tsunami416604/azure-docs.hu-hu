---
title: Monitorozás és diagnosztika az Azure Service Fabric-háló alkalmazásokban |} A Microsoft Docs
description: További tudnivalók figyelése és diagnosztizálása a Service Fabric háló az Azure-ban az alkalmazás.
services: service-fabric-mesh
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 36c9a5d75c4a72365638619ab85d451df647feb3
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939824"
---
# <a name="monitoring-and-diagnostics"></a>Monitorozás és diagnosztika
Az Azure Service Fabric Mesh egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a fejlesztők számára a mikroszolgáltatás-alkalmazások üzembe helyezését a virtuális gépek, a tárolók és a hálózat kezelése nélkül. Monitorozás és diagnosztika a Service Fabric-háló kategorizálva be a diagnosztikai adatok három fő típusa:

- Protokoly aplikací – ezek a naplók a tárolóalapú alkalmazásokat, hogyan van kialakítva az alkalmazás alapján vannak meghatározva (pl. docker-naplók)
- Platform-események – a háló releváns a tároló működés, platformról események jelenleg beleértve a tároló aktiválási, Inaktiválási és felmondás.
- Tárolómetrikák - erőforrás kihasználtságát és a teljesítmény-mérőszámok a tárolókhoz (docker statisztika)

Ez a cikk ismerteti a monitorozási és diagnosztikai lehetőségei a legújabb előzetes verziót.

## <a name="application-logs"></a>Alkalmazásnaplók

A docker-naplók megtekintéséhez a az üzembe helyezett tárolókkal, egy tárolóban történik. A Service Fabric-háló alkalmazásmodell az egyes tárolók csomag egy kódot az alkalmazásban. A kód csomaggal társított naplók megtekintéséhez használja a következő parancsot:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> A "az háló és a replikakiszolgáló" paranccsal kérje le a replika nevét. Másodpéldány nevének növekvő egész számok 0-tól.

Megjelenése a szavazóalkalmazás VotingWeb.Code tárolótól naplóinak megtekintése az itt látható:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>Tárolómetrikák 

A háló környezet elérhetővé teszi a minősített metrikák, amely azt jelzi, hogyan hajtja végre a tárolókat. A következő metrikák érhetők el az Azure portal és az Azure CLI figyelése:

| Metrika | Leírás | Egység|
|----|----|----|
| CpuUtilization | ActualCpu/AllocatedCpu as a percentage | % |
| MemoryUtilization | ActualMem/AllocatedMem százalékos | % |
| AllocatedCpu | Azure Resource Manager-sablon alapján kiosztott processzor | Millicore |
| AllocatedMemory | Az Azure Resource Manager-sablon alapján lefoglalt memória | MB |
| ActualCpu | Processzorhasználat | Millicore |
| ActualMemory | Memóriahasználat | MB |
| ContainerStatus | 0 – érvénytelen: A tároló állapota ismeretlen <br> 1 – függőben: A tároló úgy van ütemezve, elindításához <br> 2 - től: A tároló elindítás folyamatban van <br> 3 – lépések: A tároló sikeresen elindult <br> 4 – Leállítás: A tároló leáll. <br> 5 – leállt: A tároló sikeresen leállt. | – |
| ApplicationStatus | 0 - unknown: Az állapota nem lekérhető <br> 1 – kész: Az alkalmazás sikeresen fut. <br> 2 – a frissítés: Frissítés folyamatban van <br> 3 - hoz létre: Az alkalmazás létrehozása folyamatban van <br> 4 – törlése: Az alkalmazás törlése folyamatban van <br> 5 – nem sikerült: Az alkalmazás telepítése nem sikerült | – |
| ServiceStatus | 0 – érvénytelen: A szolgáltatás jelenleg nem rendelkezik egy állapot <br> 1. ok: A szolgáltatás állapota megfelelő  <br> 2 – figyelmeztetés: Előfordulhat, valami hiba történt a vizsgálatot igénylő <br> 3 – hiba: Valami nincs rendben, amely a platformnaplók vizsgálata szükséges <br> 4 – ismeretlen: Az állapota nem lekérhető | – |
| ServiceReplicaStatus | 0 – érvénytelen: A replika jelenleg nem rendelkezik egy állapot <br> 1. ok: A szolgáltatás állapota megfelelő  <br> 2 – figyelmeztetés: Előfordulhat, valami hiba történt a vizsgálatot igénylő <br> 3 – hiba: Valami nincs rendben, amely a platformnaplók vizsgálata szükséges <br> 4 – ismeretlen: Az állapota nem lekérhető | – | 
| RestartCount | Tároló újraindítások száma | – |

> [!NOTE]
> A ServiceStatus és ServiceReplicaStatus értékei ugyanazok, mint a [HealthState](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet) a Service Fabricben. 

Mindegyik metrikát eltérő dimenziókkal érhető el, így láthatja a különböző szinteken összesítések. A dimenzió aktuális listáját a következők:

* Alkalmazásnév
* ServiceName
* ServiceReplicaName
* CodePackageName

> [!NOTE]
> Linuxos alkalmazások CodePackageName dimenzió nem érhető el. 

Minden dimenzió különböző összetevői felel meg a [Service Fabric-alkalmazás modell](service-fabric-mesh-service-fabric-resources.md#applications-and-services)

### <a name="azure-monitor-cli"></a>Az Azure Monitor parancssori felület

Parancsok teljes listája megtalálható a [Azure Monitor parancssori Felülettel kapcsolatos dokumentumok](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) , de néhány hasznos az alábbi példa tartalmazza 

Minden példánál az erőforrás-azonosító ezt a mintát követi

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* Az alkalmazások a tároló CPU-kihasználtság

```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* Minden egyes szolgáltatás replika memóriahasználat
```cli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* Az egyes tárolók egy 1 óra ablakban újraindítása 
```cli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* Átlagos processzorhasználat szolgáltatások nevű "VotingWeb" ablakban 1 óra
```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>Metrikaböngésző

Metrikaböngésző panel a portálon, amelyben a háló alkalmazás segítségével megjelenítheti a mérőszámok. Ezen a panelen érhető el az alkalmazás lapján a portálon és az Azure monitor panelt, az utóbbi, amelynek segítségével az Azure-erőforrások, amelyek támogatják az Azure Monitor metrikáinak megtekintéséhez. 

![Metrikaböngésző](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>További lépések
* A Service Fabric Meshsel kapcsolatos további információkért olvassa el a [Service Fabric Mesh áttekintésével](service-fabric-mesh-overview.md) foglalkozó cikket.
* Az Azure Monitor-metrikák parancsokkal kapcsolatos további tudnivalókért tekintse meg a [Azure Monitor parancssori Felülettel kapcsolatos dokumentumok](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list).
