---
title: Az Azure Service Fabric-csomóponttípusok és virtuálisgép-méretezési csoportokban |} A Microsoft Docs
description: Ismerje meg, milyen típusú vonatkoznak a virtuálisgép-méretezési csoport Azure Service Fabric-csomópont állítja be, és távolról csatlakozni a méretezési csoport példány vagy csomópont.
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: chackdan
ms.openlocfilehash: 7f9397ee21f74fe6a776881940e5721264216b0f
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58660614"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Az Azure Service Fabric-csomóponttípusok és virtuálisgép-méretezési csoportokban
[A Virtual machine scale sets](/azure/virtual-machine-scale-sets) az Azure-beli számítási erőforrás. Használhatja a méretezési csoportok üzembe helyezése és kezelése a virtuális gépek csoportként gyűjteménye. Mindegyik csomóponttípus, amelyeket egy Azure Service Fabric-fürtöt állít be egy külön méretezési csoportban.  Állítsa be a Service Fabric-futtatókörnyezet, a méretezési csoportban lévő mindegyik virtuális gépre telepíteni. Is egymástól függetlenül mindegyik csomóponttípus kisebbre vagy nagyobbra méretezhetők, módosítsa az operációs rendszer Termékváltozata a fürt minden csomópontján fut, más-más részhalmazához nyitott portokkal rendelkezik és használni a különböző kapacitási mérőszámot.

A következő ábrán látható, amely két csomópontot típusai, FrontEnd és BackEnd nevű fürt. Mindegyik csomóponttípus öt csomópontot tartalmaz.

![A fürt, amelyben két csomópont típusa][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Virtuális gép méretezési csoport példányaihoz leképezése csomópontok
Az előző ábrán látható, a méretezési csoport példányaihoz számozása 0 példány, és növelje meg az 1. A csomópont nevének számozására tükrözi. Például BackEnd_0 csomópont a háttérrendszer méretezési 0 példányát. Az adott méretezési csoportot öt példányban BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 és BackEnd_4 nevű rendelkezik.

Amikor egy méretezési csoportot, egy új példánya jön létre. Az új méretezési csoportot példány neve általában a méretezési csoport nevét, valamint a következő példányok számát. Ebben a példában BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Méretezési csoport terheléselosztók leképezése csomóponttípusok és méretezési csoportok
Ha a fürtöt az Azure Portalon, illetve a minta az Azure Resource Manager-sablont használja, minden erőforrás egy erőforráscsoportba tartozó jelennek meg. Láthatja, hogy az egyes méretezési készlet vagy csomópont a terheléselosztók. A terheléselosztó neve a következő formátumot használja: **LB -&lt;csomóponttípus neve&gt;**. Ilyen például, az LB-sfcluster4doc-0, az alábbi ábrán látható módon:

![További források][Resources]


## <a name="next-steps"></a>További lépések
* Tekintse meg a [áttekintése a "Üzembe helyezés bárhol" funkció és az Azure által felügyelt fürtöket való összehasonlítást](service-fabric-deploy-anywhere.md).
* Ismerje meg [biztonsági fürt](service-fabric-cluster-security.md).
* [Távoli csatlakozás](service-fabric-cluster-remote-connect-to-azure-cluster-node.md) adott scale set-példányra
* [Az RPD-porttartomány értékeinek frissítése](./scripts/service-fabric-powershell-change-rdp-port-range.md) fürtön, virtuális gépek üzembe helyezés után
* [Módosítsa a rendszergazdai felhasználónevét és jelszavát](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) fürt virtuális gépeihez

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
