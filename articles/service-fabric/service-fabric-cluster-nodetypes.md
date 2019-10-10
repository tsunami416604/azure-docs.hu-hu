---
title: Azure Service Fabric csomópontok típusai és virtuálisgép-méretezési csoportok | Microsoft Docs
description: Ismerje meg, hogyan kapcsolódnak az Azure Service Fabric Node-típusok a virtuálisgép-méretezési csoportokhoz, és hogyan lehet távolról csatlakozni a méretezési csoport példányaihoz vagy a fürtcsomópontokon.
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
ms.author: pepogors
ms.openlocfilehash: cec134f9e71f86cd0ed17912f1a3c76adc9a4164
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72167326"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Azure Service Fabric Node-típusok és virtuálisgép-méretezési csoportok
A [virtuálisgép-méretezési](/azure/virtual-machine-scale-sets) csoportok egy Azure számítási erőforrás. A méretezési csoportok segítségével virtuális gépek gyűjteményét telepítheti és kezelheti készletként. Az Azure Service Fabric-fürtben definiált egyes csomópont-típusok külön méretezést állítanak be.  A Microsoft. Azure. ServiceFabric virtuálisgép-bővítmény által a méretezési csoportba tartozó egyes virtuális gépekre telepített Service Fabric futtatókörnyezet. Az egyes csomópont-típusok egymástól függetlenül méretezhetők, az egyes fürtcsomópontokon futó operációs rendszerbeli SKU-t módosíthatja, különböző portokat nyithat meg, és különböző kapacitási metrikákat használhat.

Az alábbi ábra egy olyan fürtöt mutat be, amely két csomópontos típust tartalmaz: előtér és háttér neve. Mindegyik csomópont típusa öt csomóponttal rendelkezik.

![Két csomóponttal rendelkező fürt][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Virtuálisgép-méretezési csoport példányainak leképezése csomópontokra
Ahogy az előző ábrán is látható, a méretezési csoport példányai a 0. példányon kezdődnek, majd 1 értékkel növekednek. A számozás a csomópontok neveiben jelenik meg. Például a Node BackEnd_0 a háttérbeli méretezési csoport 0. példánya. Ennek az adott méretezési csoportnak öt példánya van, amelyek neve BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 és BackEnd_4.

Méretezési csoport skálázásakor létrejön egy új példány. Az új méretezési csoport példányának neve általában a méretezési csoport neve és a következő példány száma. A példánkban ez a BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Méretezési készlet terheléselosztása a csomópontok típusaihoz és a méretezési csoportokhoz
Ha központilag telepítette a fürtöt a Azure Portal vagy használta a minta Azure Resource Manager sablont, a rendszer az erőforráscsoport összes erőforrását listázza. Az egyes méretezési csoportokhoz vagy csomópont-típusokhoz tartozó terheléselosztó megtekinthető. A terheléselosztó neve a következő formátumot használja: **LB-&lt;node Type Name @ no__t-2**. Ilyen például az LB-sfcluster4doc-0, ahogy az a következő ábrán látható:

![Segédanyagok és eszközök][Resources]

## <a name="service-fabric-virtual-machine-extension"></a>Virtuálisgép-bővítmény Service Fabric
Service Fabric a virtuálisgép-bővítmény az Azure-beli Virtual Machines rendszerindítására Service Fabric és a csomópontok biztonságának konfigurálására szolgál.

Az alábbi kódrészlet Service Fabric virtuálisgép-bővítményt mutat be:

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

A tulajdonságok leírása a következő:

| **Name (Név)** | **Megengedett értékek** | ** --- ** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
| név | sztring | --- | kiterjesztés egyedi neve |
| type | "ServiceFabricLinuxNode" vagy "ServiceFabricWindowsNode" | --- | Meghatározza, hogy az operációs rendszer Service Fabric |
| autoUpgradeMinorVersion | igaz vagy hamis | --- | Az SF Runtime másodlagos verzióinak automatikus frissítésének engedélyezése |
| Publisher | Microsoft. Azure. ServiceFabric | --- | a Service Fabric-kiterjedési közzétevő neve |
| clusterEndpont | sztring | --- | URI: PORT – felügyeleti végpont |
| nodeTypeRef | sztring | --- | nodeType neve |
| durabilityLevel | bronz, ezüst, arany, platina | --- | a nem módosítható Azure-infrastruktúra szüneteltetésének ideje |
| enableParallelJobs | igaz vagy hamis | --- | A számítási ParallelJobs engedélyezése, például a virtuális gép eltávolítása és a virtuális gép újraindítása ugyanabban a méretezési csoportba párhuzamosan |
| nicPrefixOverride | sztring | --- | Alhálózat-előtag, például "10.0.0.0/24" |
| commonNames | karakterlánc [] | --- | Telepített fürtözött tanúsítványok köznapi nevei |
| x509StoreName | sztring | --- | Azon áruház neve, ahol a telepített fürt tanúsítványa található |
| typeHandlerVersion | 1.1 | --- | A bővítmény verziója. 1,0 a bővítmény klasszikus verziója javasolt a 1,1-es verzióra való frissítésre |
| dataPath | sztring | --- | Az Service Fabric rendszerszolgáltatások és alkalmazásadatok állapotának mentéséhez használt meghajtó elérési útja. 

## <a name="next-steps"></a>Következő lépések
* Tekintse meg a ["bárhonnan üzembe helyezhető" funkció áttekintését, valamint az Azure által felügyelt fürtökkel való összehasonlítást](service-fabric-deploy-anywhere.md).
* További információ a [fürt biztonságáról](service-fabric-cluster-security.md).
* [Távoli kapcsolódás](service-fabric-cluster-remote-connect-to-azure-cluster-node.md) egy adott méretezési csoport példányaihoz
* [Az RDP-porttartomány értékének frissítése](./scripts/service-fabric-powershell-change-rdp-port-range.md) a fürtön futó virtuális gépeken az üzembe helyezést követően
* A fürt virtuális gépei [rendszergazdai felhasználónevének és jelszavának módosítása](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md)

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
