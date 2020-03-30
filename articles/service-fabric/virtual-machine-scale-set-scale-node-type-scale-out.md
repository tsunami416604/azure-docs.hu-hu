---
title: Csomóponttípus hozzáadása egy Azure Service Fabric-fürthöz
description: Ismerje meg, hogyan horizontális felskálázható egy service fabric-fürt egy virtuálisgép-méretezési csoport hozzáadásával.
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: 1e7bae89561d43d717eb4d15e95183761b077443
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463972"
---
# <a name="scale-a-service-fabric-cluster-out-by-adding-a-virtual-machine-scale-set"></a>Service Fabric-fürt méretezése virtuálisgép-méretezési csoport hozzáadásával
Ez a cikk ismerteti, hogyan skálázhatja az Azure Service Fabric-fürt egy új csomóponttípus hozzáadása egy meglévő fürthöz. A Service Fabric-fürt olyan virtuális vagy fizikai gépek hálózathoz csatlakoztatott készlete, amelybe a mikroszolgáltatások üzembe helyezése és kezelése történik. Egy gép vagy virtuális gép, amely egy fürt része, nevezzük csomópont. A virtuálisgép-méretezési csoportok egy Azure-számítási erőforrás, amely et a virtuális gépek gyűjteményének készletként történő üzembe helyezéséhez és kezeléséhez használja. Az Azure-fürtben definiált minden csomóponttípus [külön méretezési csoportként van beállítva.](service-fabric-cluster-nodetypes.md) Ezután minden csomóponttípus külön-külön kezelhető. A Service Fabric-fürt létrehozása után vízszintesen skálázhatja a fürtöt egy új csomóponttípus (virtuálisgép-méretezési csoport) hozzáadásával egy meglévő fürthöz.  A fürt bármikor méretezhető, még akkor is, ha a számítási feladatok futnak a fürtön.  A fürt méretezése, az alkalmazások automatikusan skálázható is.

## <a name="add-an-additional-scale-set-to-an-existing-cluster"></a>További méretezési csoport hozzáadása meglévő fürthöz
Új csomóponttípus hozzáadása (amelyet egy virtuálisgép-méretezési csoport támogat) egy meglévő fürthöz hasonló [az elsődleges csomóponttípus frissítéséhez,](service-fabric-scale-up-node-type.md)kivéve, hogy nem fogja ugyanazt a NodeTypeRef-et használni; nyilvánvalóan nem fogja letiltani az aktívan használt virtuálisgép-méretezési csoportokat, és nem veszíti el a fürt rendelkezésre állását, ha nem frissíti az elsődleges csomópont típusát. 

A NodeTypeRef tulajdonság deklarálva van a Service Fabric bővítmény tulajdonságaiban:
```json
<snip>
"publisher": "Microsoft.Azure.ServiceFabric",
     "settings": {
     "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
     "nodeTypeRef": "[parameters('vmNodeType2Name')]",
     "dataPath": "D:\\\\SvcFab",
     "durabilityLevel": "Silver",
<snip>
```

Ezenkívül hozzá kell adnia ezt az új csomóponttípust a Service Fabric-fürterőforráshoz:

```json
<snip>
"nodeTypes": [
      {
      "name": "[parameters('vmNodeType2Name')]",
      "applicationPorts": {
                "endPort": "[parameters('nt2applicationEndPort')]",
                "startPort": "[parameters('nt2applicationStartPort')]"
      },
      "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
      "durabilityLevel": "Silver",
       "ephemeralPorts": {
                "endPort": "[parameters('nt2ephemeralEndPort')]",
                "startPort": "[parameters('nt2ephemeralStartPort')]"
      },
      "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
      "isPrimary": false,
      "vmInstanceCount": "[parameters('nt2InstanceCount')]"
},
<snip>
```

## <a name="next-steps"></a>További lépések
* További információ [az elsődleges csomóponttípus méretezése](service-fabric-scale-up-node-type.md)
* További információ az [alkalmazások méretezhetőségéről.](service-fabric-concepts-scalability.md)
* [Azure-fürt méretezése be- és ki.](service-fabric-tutorial-scale-cluster.md)
* [Az Azure-fürtöket programozott módon méretezzük](service-fabric-cluster-programmatic-scaling.md) a gördülékeny Azure-beli SDK használatával.
* [Önálló fürt méretezése be- és kiméretezése](service-fabric-cluster-windows-server-add-remove-nodes.md).

