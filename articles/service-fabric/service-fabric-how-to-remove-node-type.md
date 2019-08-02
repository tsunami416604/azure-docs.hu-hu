---
title: Csomópont-típus eltávolítása az Azure Service Fabricban | Microsoft Docs
description: Megtudhatja, hogyan távolíthat el egy csomópont-típust egy Azure-ban futó Service Fabric-fürtből.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chakdan
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/14/2019
ms.author: atsenthi
ms.openlocfilehash: 44f25adf4168f4339a31e9270c2b23a8466a8889
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599491"
---
# <a name="remove-a-service-fabric-node-type"></a>Service Fabric csomópont típusának eltávolítása
Ez a cikk azt ismerteti, hogyan méretezhető egy Azure Service Fabric-fürt egy meglévő csomópont-típus fürtből való eltávolításával. A Service Fabric-fürt olyan virtuális vagy fizikai gépek hálózathoz csatlakoztatott készlete, amelybe a rendszer üzembe helyezi és kezeli a szolgáltatásait. Egy fürt részét képező gépet vagy virtuális gépet csomópontnak nevezzük. A virtuálisgép-méretezési csoportok egy Azure-beli számítási erőforrás, amely készletként telepíti és felügyeli a virtuális gépek gyűjteményét. Az Azure-fürtben definiált összes csomópont-típus [külön méretezési csoportként van beállítva](service-fabric-cluster-nodetypes.md). Ezután mindegyik csomópont-típust külön lehet kezelni. Service Fabric-fürt létrehozása után vízszintesen méretezheti a fürtöt egy csomópont-típus (virtuálisgép-méretezési csoport) és annak összes csomópontjának eltávolításával.  A fürtöt bármikor méretezheti, még akkor is, ha a munkaterhelések futnak a fürtön.  A fürt skálázása esetén az alkalmazások is automatikusan méretezhetők.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A [Remove-AzServiceFabricNodeType](https://docs.microsoft.com/powershell/module/az.servicefabric/remove-azservicefabricnodetype) használatával távolítson el egy Service Fabric csomópont-típust.

A Remove-AzServiceFabricNodeType hívásakor előforduló három művelet a következő:
1.  A rendszer törli a virtuálisgép-méretezési készletet a csomópont típusa mögött.
2.  A csomópont típusa el lesz távolítva a fürtből.
3.  A csomópont összes csomópontja esetében a csomópont teljes állapota törlődik a rendszerből. Ha vannak szolgáltatások az adott csomóponton, akkor a szolgáltatások először egy másik csomópontra kerülnek. Ha a Fürtfelügyelő nem talál csomópontot a replika/szolgáltatás számára, a művelet késleltetve vagy Letiltva lesz.

> [!WARNING]
> Ha a Remove-AzServiceFabricNodeType használatával távolítja el a csomópont típusát egy éles fürtből, nem ajánlott gyakran használni. Ez egy veszélyes parancs, mivel törli a virtuálisgép-méretezési csoport erőforrását a csomópont típusa mögött. 

## <a name="durability-characteristics"></a>Tartóssági jellemzők
A Remove-AzServiceFabricNodeType használata esetén a biztonság prioritást élvez a sebességnél. A csomópont típusának ezüst vagy arany [tartóssági szintűnek](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)kell lennie, mert:
- A bronz nem nyújt garanciát az állapotadatok mentésével kapcsolatban.
- Az ezüst és az arany tartóssága a méretezési csoport minden módosítását elvégzi.
- Az arany a méretezési csoport alatti Azure-frissítések felügyeletét is lehetővé teszi.

Service Fabric "összehangolja" a mögöttes változásokat és frissítéseket, így az adatvesztés nem vész el. Ha azonban egy bronz tartósságú csomópontot távolít el, az állapotadatok elvesztésével járhat. Ha eltávolít egy elsődleges csomópont-típust, és az alkalmazása állapot nélküli, a bronz elfogadható. Az állapot-nyilvántartó számítási feladatok éles környezetben való futtatásakor a minimális konfigurációnak ezüst típusúnak kell lennie. Az éles környezetekhez hasonlóan az elsődleges csomópont típusa mindig ezüst vagy arany lehet.

### <a name="more-about-bronze-durability"></a>További információ a bronz tartósságról

A bronz típusú csomópontok eltávolításakor a csomópont összes csomópontja azonnal leáll. A Service Fabric nem rendelkezik a bronz csomópontok méretezési csoportjának frissítéseivel, így az összes virtuális gép azonnal leáll. Ha a csomópontok állapota nem megfelelő, az adat elvész. Most még ha állapot nélküli is volt, a Service Fabric összes csomópontja részt vesz a gyűrűn, így a teljes környék elvész, ami a fürt destabilizálása lehet.

## <a name="recommended-node-type-removal-process"></a>Javasolt típusú csomópont-eltávolítási folyamat

A csomópont típusának eltávolításához futtassa a [Remove-AzServiceFabricNodeType](/powershell/module/az.servicefabric/remove-azservicefabricnodetype) parancsmagot.  A parancsmag végrehajtása hosszabb időt vesz igénybe.  Ha az összes virtuális gép el lett mentve ("Down"-ként jelenik meg), a Fabric:/System/InfrastructureService/[NodeType name] hibaüzenetet jelenít meg.

```powershell
$groupname = "mynodetype"
$nodetype = "nt2vm"
$clustername = "mytestcluster"

Remove-AzServiceFabricNodeType -Name $clustername  -NodeType $nodetype -ResourceGroupName $groupname

Connect-ServiceFabricCluster -ConnectionEndpoint mytestcluster.eastus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <thumbprint> `
          -FindType FindByThumbprint -FindValue <thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```

Ezután frissítheti a fürterőforrás-t a csomópont típusának eltávolításához. Használhatja az ARM-sablon üzembe helyezését, vagy szerkesztheti a fürterőforrás-t az [Azure Resource Managerrel](https://resources.azure.com). Ezzel elindítja a fürt frissítését, amely a hibás állapotú háló:/System/InfrastructureService/[NodeType name] szolgáltatást fogja eltávolítani.

A csomópontok továbbra is megjelennek a Service Fabric Explorer. Futtassa a [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) parancsot minden olyan csomóponton, amelyet el szeretne távolítani.


```powershell
$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="next-steps"></a>További lépések
- További információ a fürt [tartóssági jellemzőiről](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).
- További információ a [csomópontok típusairól és Virtual Machine Scale sets](service-fabric-cluster-nodetypes.md).
- További információ a [Service Fabric-fürt skálázásáról](service-fabric-cluster-scaling.md).
