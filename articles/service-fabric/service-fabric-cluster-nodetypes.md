---
title: Azure Service Fabric csomóponttípusok és a virtuális gép méretezési készletek |} Microsoft Docs
description: Megtudhatja, hogyan típusok kapcsolódnak a virtuálisgép-méretezési Azure Service Fabric-csomópont állítja be, és távolról csatlakoztatása egy méretezési állítsa be a példány vagy fürtcsomóponton.
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: chackdan
ms.openlocfilehash: 84d7f407781f09fed4667a22f0a46bc72c6e02a9
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212364"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Azure Service Fabric csomóponttípusok és a virtuális gép skálázása beállítása
[Virtuálisgép-méretezési csoportok](/azure/virtual-machine-scale-sets) az Azure számítási erőforrás. Méretezési csoportok segítségével telepíthetnek és kezelhetnek olyan virtuális gépek gyűjteménye. Az egyes csomóponttípusok, megadhat egy Azure Service Fabric-fürt állít be egy külön skála.  Állítsa be a Service Fabric-futtatókörnyezet, a rendszer minden egyes virtuális gépen telepítve. Lehet egymástól függetlenül az egyes csomóponttípusok méretezése felfelé vagy lefelé, módosítsa az operációs rendszer Termékváltozata minden fürtcsomóponton futó, eltérő számú portok nyitva, és használni különböző teljesítmény-mérőszámait.

Az alábbi ábrán látható, amely két csomópont típusai, előtér- és háttérszolgáltatások nevű fürt. Minden csomópont az öt csomóponttal rendelkezik.

![Egy fürt, amely két csomópont tartozik][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Virtuálisgép-méretezési készlet példányok leképezése csomópontok
A fenti ábrán látható, méretezési készlet példányok példányt 0 kezdjék, és növelje meg az 1. A csomópont nevének számozására is megjelenik. Csomópont BackEnd_0 például a háttér-méretezési csoport 0 példány. Ez különösen méretezési öt példánya, BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 és BackEnd_4 nevű van.

A méretezési növelheti, ha egy új példánya jön létre. A méretezés új példány nevének általában a virtuális gépek méretezési csoportjának név, valamint a következő példányok számát. A jelen példában BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Méretezési készlet terheléselosztók leképezése csomóponttípusok, a méretezés beállítása
Ha az Azure portálon lévő telepítve, vagy használja a minta Azure Resource Manager-sablon, egy erőforráscsoportba tartozó összes erőforrást találhatók. Megtekintheti az egyes méretezési készlet vagy csomópont azokat a terheléselosztókat. A terheléselosztó neve a következő formátumot használja: **LB -&lt;csomóponttípus&gt;**. Példa: LB-sfcluster4doc-0, a következő ábrán látható módon:

![További források][Resources]


## <a name="next-steps"></a>További lépések
* Tekintse meg a [a "Üzembe helyezés bárhol" szolgáltatás és az Azure által kezelt fürtökkel összehasonlítása áttekintése](service-fabric-deploy-anywhere.md).
* További tudnivalók [fürt biztonsági](service-fabric-cluster-security.md).
* [Távoli kapcsolódás](service-fabric-cluster-remote-connect-to-azure-cluster-node.md) egy adott méretezési készlet példányhoz
* [Frissítse a RDP-portok tartományát értékei](./scripts/service-fabric-powershell-change-rdp-port-range.md) fürtön, virtuális gépek telepítése után
* [A rendszergazda felhasználónév és jelszó módosítása](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) fürt virtuális gépek

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
