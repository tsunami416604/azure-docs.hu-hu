---
title: Az Azure Service Fabric-csomóponttípus eltávolítása |} A Microsoft Docs
description: Ismerje meg, hogy a csomópont típusa törlése az Azure-ban futó Service Fabric-fürtön.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chakdan
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/14/2019
ms.author: aljo
ms.openlocfilehash: 193a24aebff8f7de60752e53bbc1b18dd5c54f33
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59051767"
---
# <a name="remove-a-service-fabric-node-type"></a>Távolítsa el a Service Fabric-csomópont típusa
Ez a cikk azt ismerteti, hogyan méretezzünk át egy Azure Service Fabric-fürtöt egy meglévő csomóponttípus eltávolítása egy fürtről. Service Fabric-fürt, amelybe mikroszolgáltatásokat helyezhet üzembe és felügyelhet virtuális vagy fizikai gépek hálózaton keresztül csatlakozó készlete áll. Egy számítógép vagy virtuális Gépet, amely egy fürt része csomópontoknak nevezzük. Virtuálisgép-méretezési csoportok olyan számítási Azure-erőforrások üzembe helyezése és kezelése a virtuális gépek gyűjteményét készletként használt. Minden csomópont-típus egy Azure-fürtön definiált [külön méretezési csoportként](service-fabric-cluster-nodetypes.md). Mindegyik csomóponttípus kezelhetők külön-külön. Egy Service Fabric-fürt létrehozását követően méretezheti a fürt vízszintesen csomópont típusa (virtuálisgép-méretezési) és az összes hozzá tartozó csomópont eltávolításával.  Méretezheti a fürt bármikor, még akkor is, ha a számítási feladatok a fürtön futnak.  A fürt skálázható, mivel az alkalmazások automatikus méretezése is.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Használat [Remove-AzServiceFabricNodeType](https://docs.microsoft.com/powershell/module/az.servicefabric/remove-azservicefabricnodetype) egy Service Fabric-csomóponttípus eltávolítása.

A Remove-AzServiceFabricNodeType meghívásakor előforduló három műveletek a következők:
1.  A csomóponttípus mögötti virtuálisgép-méretezési törlődik.
2.  A csomóponttípus távolítja el a fürtöt.
3.  Az adott csomóponttípus lévő összes csomópont a csomóponthoz tartozó teljes állapot eltávolítani a rendszerből. Ha ezen a csomóponton szolgáltatásokat, majd a szolgáltatások először kerülnek egy másik csomópontra. A kezelőt nem található a csomópont a replika vagy szolgáltatások, a művelet, késleltetett/blokkolja-e.

> [!WARNING]
> Typ uzlu eltávolítása egy éles fürtöt Remove-AzServiceFabricNodeType használatával nem ajánlott a gyakran használható. Ez megegyezik a veszélyes parancs törli a virtuális gép méretezési csoport erőforrás mögött typ uzlu. 

## <a name="durability-characteristics"></a>Tartóssági jellemzői
Biztonsági van szándék sebességét, Remove-AzServiceFabricNodeType használatakor. A csomópont típusúnak kell lennie Silver vagy Gold [tartóssági szint](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster), mert:
- Bronz nem ad meg semmilyen garanciát kapcsolatos állapotinformációkat mentése.
- Silver és Gold tartóssági trap módosításokat a méretezési csoporthoz.
- Gold ad is szabályozhatja az Azure frissíti a méretezési csoport alá.

A Service Fabric "összehangolja" mögöttes módosításokat, és frissíti, hogy ne legyen adatvesztés. Amikor eltávolít egy csomópontot a bronz tartóssági, azonban előfordulhat, hogy állapot adat elvész. Ha szeretne eltávolítani egy elsődleges csomóponttípus, és az alkalmazás állapot nélküli, bronz fogadható el. Éles környezetben állapotalapú alkalmazások és szolgáltatások futtatásakor, a minimális konfigurációs Silver kell lennie. Hasonlóképpen a termelési forgatókönyvekhez az elsődleges csomóponttípushoz mindig lehet Silver vagy Gold.

### <a name="more-about-bronze-durability"></a>További információ a bronz tartóssági

Egy csomópont típusa, amely bronz eltávolításakor a csomópontokon írja be a csomópont leáll azonnal. A Service Fabric nem trap bronz csomópontok scale set frissítéseket, így minden virtuális gép leáll azonnal. Ha bármi állapot-nyilvántartó a csomópontokon, az adat elvész. Most, még akkor is, ha az állapot nélküli volt, a Service Fabric összes csomópontja részt a kört, így előfordulhat, hogy egy teljes hálózatok elvesztek, amely instabillá teheti a fürthöz.

## <a name="recommended-node-type-removal-process"></a>Csomópont típusa eltávolítást javasolt

A csomóponttípus eltávolításához futtassa a [Remove-AzServiceFabricNodeType](/powershell/module/az.servicefabric/remove-azservicefabricnodetype) parancsmagot.  A parancsmag befejezése hosszabb időt vesz igénybe.  Ezután futtassa [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) egyes az eltávolítani kívánt csomópontokat.

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

$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="next-steps"></a>További lépések
- További tudnivalók a fürt [tartóssági jellemzők](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).
- Tudjon meg többet [csomóponttípusok és virtuálisgép-méretezési csoportok](service-fabric-cluster-nodetypes.md).
- Tudjon meg többet [Service Fabric-fürt méretezése](service-fabric-cluster-scaling.md).
