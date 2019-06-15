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
ms.openlocfilehash: a5f8735df2b230de2b0ddcdcccff09430bada9e3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64684693"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Az Azure Service Fabric-csomóponttípusok és virtuálisgép-méretezési csoportokban
[A Virtual machine scale sets](/azure/virtual-machine-scale-sets) az Azure-beli számítási erőforrás. Használhatja a méretezési csoportok üzembe helyezése és kezelése a virtuális gépek csoportként gyűjteménye. Mindegyik csomóponttípus, amelyeket egy Azure Service Fabric-fürtöt állít be egy külön méretezési csoportban.  A Service Fabric-futtatókörnyezet Microsoft.Azure.ServiceFabric virtuálisgép-bővítmény a méretezési minden egyes virtuális gépre telepíteni. Is egymástól függetlenül mindegyik csomóponttípus kisebbre vagy nagyobbra méretezhetők, módosítsa az operációs rendszer Termékváltozata a fürt minden csomópontján fut, más-más részhalmazához nyitott portokkal rendelkezik és használni a különböző kapacitási mérőszámot.

A következő ábrán látható, amely két csomópontot típusai, FrontEnd és BackEnd nevű fürt. Mindegyik csomóponttípus öt csomópontot tartalmaz.

![A fürt, amelyben két csomópont típusa][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Virtuális gép méretezési csoport példányaihoz leképezése csomópontok
Az előző ábrán látható, a méretezési csoport példányaihoz számozása 0 példány, és növelje meg az 1. A csomópont nevének számozására tükrözi. Például BackEnd_0 csomópont a háttérrendszer méretezési 0 példányát. Az adott méretezési csoportot öt példányban BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 és BackEnd_4 nevű rendelkezik.

Amikor egy méretezési csoportot, egy új példánya jön létre. Az új méretezési csoportot példány neve általában a méretezési csoport nevét, valamint a következő példányok számát. Ebben a példában BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Méretezési csoport terheléselosztók leképezése csomóponttípusok és méretezési csoportok
Ha a fürtöt az Azure Portalon, illetve a minta az Azure Resource Manager-sablont használja, minden erőforrás egy erőforráscsoportba tartozó jelennek meg. Láthatja, hogy az egyes méretezési készlet vagy csomópont a terheléselosztók. A terheléselosztó neve a következő formátumot használja: **LB -&lt;csomóponttípus neve&gt;** . Ilyen például, az LB-sfcluster4doc-0, az alábbi ábrán látható módon:

![További források][Resources]

## <a name="service-fabric-virtual-machine-extension"></a>Service Fabric Virtual Machine Extension
Service Fabric virtuálisgép-bővítmény segítségével elindíthat a Service Fabric az Azure Virtual Machines és a csomópont biztonságának konfigurálása.

Az alábbi kódrészlettel, amely a Service Fabric virtuálisgép-bővítményt:

```json
"extensions": [
  {
    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
    "properties": {
      "type": "ServiceFabricLinuxNode",
      "autoUpgradeMinorVersion": true,
      "protectedSettings": {
        "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
       },
       "publisher": "Microsoft.Azure.ServiceFabric",
       "settings": {
         "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
         "nodeTypeRef": "[variables('vmNodeType0Name')]",
         "durabilityLevel": "Silver",
         "enableParallelJobs": true,
         "nicPrefixOverride": "[variables('subnet0Prefix')]",
         "certificate": {
           "commonNames": [
             "[parameters('certificateCommonName')]"
           ],
           "x509StoreName": "[parameters('certificateStoreValue')]"
         }
       },
       "typeHandlerVersion": "1.1"
     }
   },
```

A tulajdonság leírása a következők:

| **Name (Név)** | **Megengedett értékek** | ** --- ** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
| name | string | --- | egyedi nevet a bővítmény |
| type | "ServiceFabricLinuxNode" vagy "ServiceFabricWindowsNode | --- | Azonosítja az operációs rendszer a Service Fabric a rendszer rendszerindítása |
| autoUpgradeMinorVersion | IGAZ vagy hamis | --- | SF futásidejű alverziót az automatikus frissítés engedélyezése |
| publisher | Microsoft.Azure.ServiceFabric | --- | a Service Fabric által közzétevő neve |
| clusterEndpont | string | --- | URI:port felügyeleti végpontja |
| nodeTypeRef | string | --- | NodeType csomóponttípus neve |
| tartóssági szint | bronz, ezüst, arany, platinum | --- | nem módosítható az Azure-infrastruktúra szüneteltetésére engedélyezett idő |
| enableParallelJobs | IGAZ vagy hamis | --- | Compute ParallelJobs, távolítsa el a virtuális gép és a virtuális Gépet a párhuzamos azonos méretezési engedélyezése |
| nicPrefixOverride | string | --- | Például a "10.0.0.0/24" alhálózati előtag |
| commonNames | String] | --- | Fürtben telepített tanúsítványok köznapi nevek |
| x509StoreName | string | --- | Ahol a fürt telepített tanúsítvány megtalálható Store neve |
| typeHandlerVersion | 1.1 | --- | Bővítmény verziója. bővítmény klasszikus verziója 1.0-ás javasolt 1.1-es frissítés |

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
