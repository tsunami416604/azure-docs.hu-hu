---
title: Service Fabric Azure Resource Manager telepítési korlátok
description: Ez a cikk áttekintést nyújt a Service Fabric-fürt azure Resource Manageren keresztül történő üzembe helyezésekor elkövetett gyakori hibákról, és ezek elkerüléséről.
services: service-fabric
documentationcenter: .net
author: peterpogorski
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: pepogors
ms.openlocfilehash: a61b0cf30ca46eb77837eb09d6a9a0b6f30e89a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368575"
---
# <a name="service-fabric-guardrails"></a>Szolgáltatás háló védőkorlátjai 
Service Fabric-fürt telepítésekor korlátok kerülnek bevezetésre, amelyek érvénytelen fürtkonfiguráció esetén sikertelenek lesznek az Azure Resource Manager üzembe helyezésekor. A következő szakaszok áttekintést nyújtanak a fürtkonfigurációval kapcsolatos gyakori problémákról és a problémák enyhítéséhez szükséges lépésekről. 

## <a name="durability-mismatch"></a>A tartóssági eltérés
### <a name="overview"></a>Áttekintés
A Service Fabric-csomóponttípus tartóssági értékét egy Azure Resource Manager-sablon két különböző szakaszában definiálja. A Virtuálisgép méretezési csoport erőforrás virtualmachine scale set bővítmény szakasza, valamint a Service Fabric fürterőforrás Csomóponttípusa szakasza. Ez a követelmény, hogy a tartóssági érték ezekben a szakaszokban egyezik, különben az erőforrás-telepítés sikertelen lesz.

A következő szakasz egy példa a virtuális gép méretezési csoport bővítmény tartóssági és a Service Fabric-csomópont típusa tartóssági beállítás közötti tartóssági eltérést tartalmazza:  

**Virtuálisgép-méretezési készlet tartóssági beállítása**
```json 
{
  "extensions": [
    {
      "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
      "properties": {
        "type": "ServiceFabricNode",
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
          "durabilityLevel": "Silver",
        }
      }
    }
  ]
}
```

**A Service Fabric csomóponttípus tartóssági beállítása** 
```json
{
  "nodeTypes": [
    {
      "name": "[variables('vmNodeType0Name')]",
      "durabilityLevel": "Bronze",
      "isPrimary": true,
      "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    }
  ]
}
```

### <a name="error-messages"></a>Hibaüzenetek
* A virtuálisgép-méretezési készlet tartóssági eltérése nem felel meg az aktuális Szolgáltatásfabric-csomóponttípus tartóssági szintjének
* A virtuálisgép-méretezési csoport tartóssága nem felel meg a cél szolgáltatásháló-csomópont típusának tartóssági szintjének
* A virtuálisgép-méretezési készlet tartóssága megfelel a Service Fabric aktuális tartóssági szintjének vagy a cél szolgáltatásfabric-csomóponttípus tartóssági szintjének 

### <a name="mitigation"></a>Kezelés
A tartóssági eltérés kijavítása, amelyet a fenti hibaüzenetek bármelyike jelez:
1. Frissítse a tartóssági szintet az Azure Resource Manager-sablon Virtuálisgép-méretezési csoport vagy a Service Fabric node type szakaszában, hogy az értékek megegyeznek.
2. Telepítse újra az Azure Resource Manager sablont a frissített értékekkel.


## <a name="seed-node-deletion"></a>A kezdő csomópontok törlése 
### <a name="overview"></a>Áttekintés
A Service Fabric-fürt rendelkezik egy [megbízhatósági szint](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) tulajdonsággal, amely a fürt elsődleges csomóponttípusán futó rendszerszolgáltatások replikáinak számát határozza meg. A szükséges replikák száma határozza meg a fürt elsődleges csomóponttípusában karbantartandó csomópontok minimális számát. Ha az elsődleges csomóponttípuscsomópont-típusok csomópontjainak száma a megbízhatósági szinthez szükséges minimum alá csökken, a fürt instabillá válik.  

### <a name="error-messages"></a>Hibaüzenetek 
A rendszer észlelte a magcsomópontok eltávolítását, és a rendszer elutasítja. 
* Ez a művelet {0} azt eredményezné, hogy csak a {1} potenciális magcsomópontok maradnak a fürtben, amíg legalább szükség van.
* A {0} magcsomópontok eltávolítása a {1} magcsomópont kvórumának elvesztése miatt lefelé haladna. Az egyszerre eltávolítható magcsomópontok maximális száma . {2}
 
### <a name="mitigation"></a>Kezelés 
Győződjön meg arról, hogy az elsődleges csomópont típusa elegendő virtuális géppel rendelkezik a fürtön megadott megbízhatósághoz. Nem fogja tudni eltávolítani a virtuális gépet, ha a virtuálisgép-méretezési készlet et az adott megbízhatósági szint minimális csomópontjainak száma alá viszi.
* Ha a megbízhatósági szint helyesen van megadva, győződjön meg arról, hogy elegendő csomópont van az elsődleges csomópont típusában, ha szükséges a megbízhatósági szinthez. 
* Ha a megbízhatósági szint helytelen, kezdeményezzen egy módosítást a Service Fabric erőforrás csökkenti a megbízhatósági szintet először a virtuális gép méretezési készlet műveletek kezdeményezése előtt, és várja meg, amíg befejeződik.
* Ha a megbízhatósági szint Bronz, kövesse ezeket a [lépéseket](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#manually-remove-vms-from-a-node-typevirtual-machine-scale-set) a fürt kecsesen történő leskálázására.

## <a name="next-steps"></a>További lépések
* Fürt létrehozása virtuális gépeken vagy Windows Server rendszert futtató számítógépeken: [Service Fabric-fürt létrehozása Windows Server rendszerhez](service-fabric-cluster-creation-for-windows-server.md)
* Fürt létrehozása virtuális gépeken vagy Linuxot futtató számítógépeken: [Linux-fürt létrehozása](service-fabric-cluster-creation-via-portal.md)
* Szolgáltatásháló – [hibaelhárítási útmutatók](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
