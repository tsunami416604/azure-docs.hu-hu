---
title: Service Fabric Azure Resource Manager üzembe helyezési guardrails
description: Ez a cikk áttekintést nyújt a Service Fabric-fürtök Azure Resource Manager használatával történő telepítésekor előforduló gyakori hibákról és azok elkerüléséről.
services: service-fabric
documentationcenter: .net
author: peterpogorski
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: pepogors
ms.openlocfilehash: fe5ff2a5eeb4b2c73165d1577702eb6af7079b61
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426739"
---
# <a name="service-fabric-guardrails"></a>Service Fabric guardrails 
Service Fabric-fürt telepítésekor a rendszer behelyezi a guardrails-t, így az érvénytelen fürtkonfiguráció esetén sikertelen lesz a Azure Resource Manager központi telepítés. A következő szakaszokban áttekintheti a fürtökkel kapcsolatos gyakori problémákat és a problémák enyhítéséhez szükséges lépéseket. 

## <a name="durability-mismatch"></a>Nem megfelelő tartósság
### <a name="overview"></a>Áttekintés
Egy Service Fabric típusú csomópont tartóssági értéke egy Azure Resource Manager sablon két különböző szakaszában van definiálva. A virtuálisgép-méretezési csoport erőforrásának virtuálisgép-méretezési csoport bővítmény szakasza, valamint a Service Fabric fürterőforrás csomópont típusa szakasza. Követelmény, hogy az ezekben a fejezetekben szereplő tartóssági érték egyezzen, ellenkező esetben az erőforrás-telepítés sikertelen lesz.

A következő szakasz a virtuálisgép-méretezési csoport bővítmény tartóssági beállításának és a Service Fabric csomópont típusú tartóssági beállításnak a tartóssági eltérését mutatja be:  

**Virtuálisgép-méretezési csoport tartóssági beállítása**
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

**Service Fabric a csomópont típusának tartóssági beállítása** 
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
* A virtuálisgép-méretezési csoport tartóssági eltérése nem egyezik a jelenlegi Service Fabric csomópont típusú tartóssági szinttel
* A virtuálisgép-méretezési csoport tartóssága nem felel meg a cél Service Fabric csomópont típusú tartóssági szintnek.
* A virtuálisgép-méretezési csoport tartóssága megfelel a jelenlegi Service Fabric tartóssági szintnek vagy a cél Service Fabric csomópont típusú tartóssági szintnek. 


### <a name="mitigation"></a>Kezelés
A fenti hibaüzenetek bármelyike által jelzett tartóssági eltérés kijavítása:
1. Módosítsa a tartóssági szintet a Azure Resource Manager sablon virtuálisgép-méretezési csoport bővítmény vagy Service Fabric csomópont típusa szakaszában, hogy az értékek megegyezzenek.
2. Telepítse újra a Azure Resource Manager sablont a frissített értékekkel.

## <a name="next-steps"></a>Következő lépések
* Fürt létrehozása a Windows Servert futtató virtuális gépeken vagy számítógépeken: [Service Fabric Windows Server-fürt létrehozása](service-fabric-cluster-creation-for-windows-server.md)
* Fürt létrehozása virtuális gépeken vagy Linuxon futó számítógépeken: [Linux-fürt létrehozása](service-fabric-cluster-creation-via-portal.md)
* Hibaelhárítási Service Fabric: [útmutatók](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
