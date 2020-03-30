---
title: Csomóponttípusok és virtuálisgép-méretezési csoportok
description: Ismerje meg, hogy az Azure Service Fabric-csomóponttípusok hogyan kapcsolódnak a virtuálisgép-méretezési csoportokhoz, és hogyan csatlakozhat távolról egy méretezési csoport példányához vagy fürtcsomóponthoz.
ms.topic: conceptual
ms.date: 03/23/2018
ms.author: pepogors
ms.custom: sfrev
ms.openlocfilehash: 37d4c27d3033545c523cefc2f317073af531f095
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78199716"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Az Azure Service Fabric-csomóponttípusok és a virtuálisgép-méretezési csoportok

[A virtuálisgép-méretezési csoportok](/azure/virtual-machine-scale-sets) egy Azure számítási erőforrás. A méretezési csoportok segítségével üzembe helyezheti és kezelheti a virtuális gépek gyűjteménye készletként. Az Azure Service Fabric-fürtben megadott minden csomóponttípus pontosan egy méretezési csoportot állít be: több csomóponttípus nem támogatható ugyanaza méretezési csoport, és egy csomóponttípust (a legtöbb esetben) nem kell több méretezési csoportnak támogatnia. Ez alól kivételt képez a csomóponttípus [vertikális skálázásának](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations) ritka esete, ha `nodeTypeRef` ideiglenesen két azonos értékű méretezési készlettel rendelkezik, miközben a replikák at az eredetiről a frissített méretezési csoportba telepíti át.

A Service Fabric futásidejű telepítve van minden virtuális gépen a *microsoft.Azure.ServiceFabric* virtuális gép bővítmény által beállított skálán. Az egyes csomóponttípusokat egymástól függetlenül skálázhatja fel- vagy le, módosíthatja az egyes fürtcsomópontokon futó operációsrendszer-termékváltozatot, különböző portkészleteket nyithat meg, és különböző kapacitásmetrikákat használhat.

Az alábbi ábrán egy fürt látható, amelykét csomóponttípussal rendelkezik, *FrontEnd* és *BackEnd*néven. Minden csomóponttípusnak öt csomópontja van.

![Két csomóponttípussal rendelkező fürt][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Virtuálisgép-méretezési példányok csomópontokhoz való leképezése

Az előző ábrán látható módon a méretezési csoport példányai a 0., majd 1-vel nőnek. A számozás a csomópontnevekben tükröződik. Például BackEnd_0 csomópont a BackEnd méretezési csoport 0 példányát. Ennek a méretezési csoportnak öt példánya van, BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 és BackEnd_4.

Méretezési csoport skálázásakor egy új példány jön létre. Az új méretezési csoport példányneve általában a méretezési csoport neve és a következő példány száma. A mi példánkban ez BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Méretezési skálázási készlet készletének leképezése csomóponttípusokhoz és méretezési készletekhez

Ha üzembe helyezte a fürtöt az Azure Portalon, vagy a minta Azure Resource Manager sablont használt, az erőforráscsoport összes erőforrása megjelenik. Megtekintheti az egyes méretezési csoportok vagy csomóponttípusok terheléselosztóit. A terheléselosztó neve a következő formátumot használja: **LB-&lt;csomóponttípus neve&gt;**. Példa erre az LB-sfcluster4doc-0, az alábbi ábrán látható módon:

![Források][Resources]

## <a name="service-fabric-virtual-machine-extension"></a>Szolgáltatásfabric virtuális gép bővítménye

A Service Fabric virtuálisgép-bővítmény használatával a Service Fabric az Azure virtuális gépek, és konfigurálja a csomópont biztonságát.

A service fabric virtuális gép bővítményének részlete a következő:

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
         "dataPath": "D:\\\\SvcFab",
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

A tulajdonság leírásai a következők:

| **Név** | **Engedélyezett értékek** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
| név | sztring | A bővítmény egyedi neve |
| type | "ServiceFabricLinuxNode" vagy "ServiceFabricWindowsNode" | Azonosítja az operációs rendszerszolgáltatás-háló tikulyát |
| autoUpgradeMinorVersion | true (igaz) vagy false (hamis) | SF futásidejű alverziók automatikus frissítésének engedélyezése |
| közzétevő | Microsoft.Azure.ServiceFabric | A Service Fabric-bővítmény közzétevőjének neve |
| fürtEndpont | sztring | URI:PORT a felügyeleti végpontba |
| nodeTypeRef | sztring | NodeType neve |
| tartósságSzint | bronz, ezüst, arany, platina | A nem módosítható Azure-infrastruktúra szüneteltetéséhez szükséges idő |
| párhuzamos feladatokkal | true (igaz) vagy false (hamis) | A Compute ParallelJobs engedélyezése, például a virtuális gép eltávolítása és a virtuális gép újraindítása ugyanabban a méretezési készletben párhuzamosan |
| nicPrefixOverride | sztring | Alhálózati előtag, például "10.0.0.0/24" |
| commonNames (közösnevek) | karakterlánc[] | A telepített fürttanúsítványok közös nevei |
| x509StoreName | sztring | Annak az üzletnek a neve, ahol a telepített fürttanúsítvány található |
| typeHandlerVersion | 1.1 | A bővítmény verziója. 1.0 klasszikus változata kiterjesztés ajánlott frissíteni 1.1 |
| dataPath (dataPath) | sztring | A Service Fabric rendszerszolgáltatások és alkalmazásadatok állapotának mentéséhez használt meghajtó elérési útja.

## <a name="next-steps"></a>További lépések

* Tekintse meg [a "Bárhol üzembe helyezés" szolgáltatás áttekintését és az Azure által felügyelt fürtökkel való összehasonlítást.](service-fabric-deploy-anywhere.md)
* További információ a [fürtbiztonságról](service-fabric-cluster-security.md).
* [Távoli csatlakozás](service-fabric-cluster-remote-connect-to-azure-cluster-node.md) egy adott méretezési csoport példányához
* [Az RDP-porttartomány értékeinek frissítése](./scripts/service-fabric-powershell-change-rdp-port-range.md) a fürt virtuális gépein a telepítés után
* [Fürtvirtuális gépek rendszergazdai felhasználónevének és jelszavának módosítása](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md)

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
