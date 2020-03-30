---
title: Figyelés és diagnosztika az Azure Service Fabric Mesh alkalmazásokban
description: Ismerje meg az alkalmazás figyelését és diagnosztizálását az Azure-beli Service Fabric Mesh alkalmazásban.
author: srrengar
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 247a1de4d00668371337295616d31caf101f0cc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75498146"
---
# <a name="monitoring-and-diagnostics"></a>Monitorozás és diagnosztika
Az Azure Service Fabric Mesh egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a fejlesztők számára a mikroszolgáltatás-alkalmazások üzembe helyezését a virtuális gépek, a tárolók és a hálózat kezelése nélkül. A Service Fabric Mesh figyelése és diagnosztikája a diagnosztikai adatok három fő típusa szerint van kategorizálva:

- Alkalmazásnaplók – ezek a tárolóba helyezett alkalmazások naplói, az alkalmazás instrumentált (pl. docker-naplók) alapján.
- Platformesemények – a hálóplatformról származó, a tároló műveletéhez kapcsolódó események, amelyek jelenleg a tároló aktiválását, az inaktiválást és a lezárást is.
- Tároló metrikák - erőforrás-kihasználtsági és teljesítménymetrikák a tárolók (docker statisztika)

Ez a cikk ismerteti a figyelési és diagnosztikai lehetőségek a legújabb előzetes verzió elérhető.

## <a name="application-logs"></a>Alkalmazásnaplók

Megtekintheti a docker-naplók az üzembe helyezett tárolók, tárolónként. A Service Fabric Mesh alkalmazásmodellben minden tároló egy kódcsomag az alkalmazásban. A kódcsomaggal társított naplók megtekintéséhez használja a következő parancsot:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> Az "Az mesh service-replica" paranccsal lekaphatja a replika nevét. A replikanevek 0-tól növekményezik az egész számokat.

Itt van, amit ez úgy néz ki, mint a látta a naplókat a VotingWeb.Code konténer a szavazási alkalmazás:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>Tároló metrikák 

A háló környezet egy maroknyi metrikát tár fel, amelyek jelzik a tárolók teljesítményét. A következő metrikák érhetők el az Azure Portalon és az Azure monitor CLI-n keresztül:

| Metrika | Leírás | Egység|
|----|----|----|
| Cpukihasználtság | ActualCpu/AllocatedCpu százalékban | % |
| Memóriakihasználtság | ActualMem/AllocatedMem százalékban | % |
| Felosztott cpu | Az Azure Resource Manager-sablon szerint lefoglalt processzor | Millicores |
| Lefoglalt memória | Az Azure Resource Manager-sablon szerint lefoglalt memória | MB |
| ActualCpu | Processzorhasználat | Millicores |
| ActualMemory (Tényleges memória) | Memóriahasználat | MB |
| ContainerStatus (Konténerállapota) | 0 - Érvénytelen: A tároló állapota ismeretlen <br> 1 - Függőben: A tároló indítása tervezett <br> 2 - Indítás: A tartály indítása folyamatban van <br> 3 - Elindult: A tároló sikeresen elindult <br> 4 - Megállás: A tartály leállítása <br> 5 - Leállítva: A tartály sikeresen leállt | N/A |
| ApplicationStatus alkalmazásállapota | 0 - Ismeretlen: Az állapot nem olvasható be <br> 1 - Kész: Az alkalmazás sikeresen fut <br> 2 - Frissítés: Van egy frissítés folyamatban <br> 3 - Létrehozása: Az alkalmazás létrehozása folyamatban van <br> 4 - Törlés: Az alkalmazás törlése folyamatban van <br> 5 - Nem sikerült: Az alkalmazás telepítése nem sikerült | N/A |
| ServiceStatus (Szolgáltatásállapota) | 0 - Érvénytelen: A szolgáltatás jelenleg nem rendelkezik állapottal <br> 1 - Ok: A szolgáltatás kifogástalan  <br> 2 - Figyelmeztetés: Lehet, hogy valami baj van igénylő vizsgálat <br> 3 - Hiba: Van valami baj, hogy vizsgálatot kell <br> 4 - Ismeretlen: Az állapot nem olvasható be | N/A |
| SzolgáltatásReplicaStatus | 0 - Érvénytelen: A replika jelenleg nem rendelkezik állapottal <br> 1 - Ok: A szolgáltatás kifogástalan  <br> 2 - Figyelmeztetés: Lehet, hogy valami baj van igénylő vizsgálat <br> 3 - Hiba: Van valami baj, hogy vizsgálatot kell <br> 4 - Ismeretlen: Az állapot nem olvasható be | N/A | 
| RestartCount (Újraindítási számláló) | A tároló újraindításainak száma | N/A |

> [!NOTE]
> A ServiceStatus és serviceReplicaStatus értékek megegyeznek a Service Fabric [HealthState](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet) értékével. 

Minden metrika különböző dimenziókban érhető el, így különböző szinteken láthatja az összesítéseket. A dimenziók aktuális listája a következő:

* ApplicationName
* ServiceName
* ServiceReplicaName
* CodePackageName (CodePackageName)

> [!NOTE]
> A CodePackageName dimenzió nem érhető el Linux-alkalmazásokhoz. 

Minden dimenzió a Service Fabric [alkalmazásmodell](service-fabric-mesh-service-fabric-resources.md#applications-and-services) különböző összetevőinek felel meg

### <a name="azure-monitor-cli"></a>Azure Monitor CLI

A parancsok teljes listája elérhető az [Azure Monitor CLI-dokumentumaiban,](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) de az alábbiakban néhány hasznos példát is bemutattunk 

Minden példában az erőforrás-azonosító ezt a mintát követi.

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* A tárolók cpu-kihasználtsága egy alkalmazásban

```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* Az egyes szolgáltatáskópiák memóriakihasználtsága
```cli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* Minden egyes tároló újraindítása 1 óra alatt 
```cli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* Átlagos CPU-kihasználtság a "VotingWeb" nevű szolgáltatások között 1 óra alatt
```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>Metrika-kezelő

Metrika-kezelő egy panel a portálon, ahol a háló alkalmazás összes metrikáját vizualizálhatja. Ez a panel érhető el az alkalmazás oldalán a portálon, és az Azure-figyelő panel, amely az utóbbi segítségével az Azure-erőforrások, amelyek támogatják az Azure Monitor metrikák. 

![Metrikaböngésző](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>További lépések
* A Service Fabric Meshsel kapcsolatos további információkért olvassa el a [Service Fabric Mesh áttekintésével](service-fabric-mesh-overview.md) foglalkozó cikket.
* Ha többet szeretne megtudni az Azure Monitor metrikák parancsairól, tekintse meg az [Azure Monitor CLI-dokumentumait.](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list)
