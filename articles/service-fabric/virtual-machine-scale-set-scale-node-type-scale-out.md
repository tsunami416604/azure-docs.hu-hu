---
title: Typ uzlu hozzáadása egy Azure Service Fabric-fürtön |} A Microsoft Docs
description: Ismerje meg, hogyan skálázhatja fel horizontálisan a Service Fabric-fürt hozzáadásával egy virtuálisgép-méretezési csoportban.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/13/2019
ms.author: aljo
ms.openlocfilehash: ed5bf829e2fbff6c286acdb21a8d0158148483d9
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662839"
---
# <a name="scale-a-service-fabric-cluster-out-by-adding-a-virtual-machine-scale-set"></a>Service Fabric-fürt ki egy virtuálisgép-méretezési csoportot hozzáadásával méretezhető
Ez a cikk ismerteti, hogyan méretezzünk át egy Azure Service Fabric-fürtöt egy meglévő fürthöz ad hozzá egy új csomópont típusa. Service Fabric-fürt, amelybe mikroszolgáltatásokat helyezhet üzembe és felügyelhet virtuális vagy fizikai gépek hálózaton keresztül csatlakozó készlete áll. Egy számítógép vagy virtuális Gépet, amely egy fürt része csomópontoknak nevezzük. Virtuálisgép-méretezési csoportok olyan számítási Azure-erőforrások üzembe helyezése és kezelése a virtuális gépek gyűjteményét készletként használt. Minden csomópont-típus egy Azure-fürtön definiált [külön méretezési csoportként](service-fabric-cluster-nodetypes.md). Mindegyik csomóponttípus kezelhetők külön-külön. Egy Service Fabric-fürt létrehozását követően akkor is egy fürt hozzáadásával horizontálisan skálázhatja egy új csomópont típusa (virtuálisgép-méretezési csoportot) egy meglévő fürthöz.  Méretezheti a fürt bármikor, még akkor is, ha a számítási feladatok a fürtön futnak.  A fürt skálázható, mivel az alkalmazások automatikus méretezése is.

## <a name="add-an-additional-scale-set-to-an-existing-cluster"></a>Adjon hozzá egy további méretezési csoport meglévő fürthöz
Egy új csomópont típusa (amely egy virtuálisgép-méretezési készlet biztonsági) hozzáadása egy meglévő fürthöz hasonlít a [történő frissítését az elsődleges csomóponttípushoz](service-fabric-scale-up-node-type.md), azzal a különbséggel nem fog használni az azonos nodetyperef hivatkozással; természetesen nem kell szükségesekre aktívan használt Virtuálisgép-méretezési csoportok, és a fürt rendelkezésre állását nem vesznek el, ha nem frissíti az elsődleges csomóponttípushoz. 

Nodetyperef hivatkozással tulajdonság lett deklarálva belül a virtuális gép méretezési Service Fabric-bővítmény tulajdonságai:
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

Emellett szüksége lesz a Service Fabric-fürt erőforrás ad hozzá az új csomópont típusa:

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
* Ismerje meg, hogyan [vertikális felskálázása az elsődleges csomópont típusa](service-fabric-scale-up-node-type.md)
* Ismerje meg [alkalmazás méretezhetőségi](service-fabric-concepts-scalability.md).
* [Egy Azure-fürtön lévő vagy horizontális skálázása](service-fabric-tutorial-scale-cluster.md).
* [Egy Azure-fürtön programozott skálázása](service-fabric-cluster-programmatic-scaling.md) az fluent Azure compute SDK-t.
* [Vagy önálló fürt méretezése](service-fabric-cluster-windows-server-add-remove-nodes.md).

