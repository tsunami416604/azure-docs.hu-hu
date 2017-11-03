---
title: "Gyermek-erőforrás meghatározásához az Azure-sablon alapján |} Microsoft Docs"
description: "Bemutatja, hogyan állítsa be az erőforrás típusát és a gyermek-erőforrás nevét az Azure Resource Manager-sablonok"
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: tomfitz
ms.openlocfilehash: 5b6ce5526f354008eb4a697deec737876f22391f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="set-name-and-type-for-child-resource-in-resource-manager-template"></a>A Resource Manager-sablon nevét és típusát gyermek erőforrás beállítása
Sablon létrehozásakor meg gyakran meg kell adnia a gyermek erőforrása, amely a szülő erőforrás kapcsolódik. A sablon tartalmazhat például egy SQL server és adatbázis. Az SQL-kiszolgáló a szülő erőforrás, az adatbázis pedig a gyermek-erőforrás. 

A gyermek erőforrástípus formátuma:`{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`

A gyermek-erőforrás neve formátuma:`{parent-resource-name}/{child-resource-name}`

Azonban adnia típusa és neve menübeállításoktól függően e beágyazott a szülő erőforrás belül, vagy a saját legfelső szintjén sablonban. Ez a témakör bemutatja, hogyan legyen kezelve mindkét megközelítés.

Amikor egy erőforrást egy teljesen minősített hivatkozást hozhat létre, típusa és neve a szegmensek egyesítése sorrendje nem egyszerűen a két összefűzése.  A névtér után inkább sorozatát *típusnév/* a legjobban megfelel a legkevésbé egyedi párok:

```json
{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]*
```

Példa:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt`megfelelő `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` helytelen

## <a name="nested-child-resource"></a>Beágyazott gyermektevékenységének erőforrás
A legegyszerűbben úgy adja meg a gyermek-erőforrás beágyazásához azt a szülő erőforrás belül. A következő példa bemutatja egy olyan SQL Server ágyazott SQL-adatbázis.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  ...
  "resources": [
    {
      "name": "exampledatabase",
      "type": "databases",
      "apiVersion": "2014-04-01",
      ...
    }
  ]
}
```

A gyermek-erőforrás, a típusuk értéke `databases` , de a teljes erőforrás típusa `Microsoft.Sql/servers/databases`. Nincs megadva `Microsoft.Sql/servers/` mivel feltételezzük, hogy a szülő erőforrás típusból. A gyermek-erőforrás neve legyen `exampledatabase` , de a teljes nevet tartalmazza a szülő nevének. Nincs megadva `exampleserver` mivel feltételezzük, hogy a szülő erőforrás.

## <a name="top-level-child-resource"></a>Legfelső szintű gyermek-erőforrás
Megadhatja, hogy a gyermek-erőforrás legfelső szintjén. Előfordulhat, hogy ezt a módszert használja, ha az a szülő erőforrás nem ugyanazt a sablont, vagy ha szeretné használni `copy` több gyermek-erőforrás létrehozása. Ezt a módszert használja adja meg a teljes erőforrás típusát, és a szülő erőforrás neve tartalmazza a gyermek-erőforrás neve.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  "resources": [ 
  ],
  ...
},
{
  "name": "exampleserver/exampledatabase",
  "type": "Microsoft.Sql/servers/databases",
  "apiVersion": "2014-04-01",
  ...
}
```

Az adatbázis egy gyermek-erőforrás a kiszolgálóra, annak ellenére, hogy a sablonban ugyanazon a szinten vannak definiálva.

## <a name="next-steps"></a>Következő lépések
* Sablonok létrehozásával kapcsolatos ajánlások, lásd: [ajánlott eljárások Azure Resource Manager-sablonok létrehozásához](resource-manager-template-best-practices.md).
* Például több gyermek erőforrások létrehozásának, [erőforrások az Azure Resource Manager sablonokban több példányának telepítése](resource-group-create-multiple.md).
