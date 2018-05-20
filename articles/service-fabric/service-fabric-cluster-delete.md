---
title: Egy Azure-fürttel és az erőforrások törlése |} Microsoft Docs
description: Ismerje meg, hogyan teljes törléséhez a Service Fabric fürt, vagy törli a fürtöt tartalmazó erőforráscsoportot, vagy szelektív módon törli az erőforrásokat.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: de422950-2d22-4ddb-ac47-dd663a946a7e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/24/2017
ms.author: aljo
ms.openlocfilehash: 55840dc4b9f25c7e2676cff936390a0542eb239d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="delete-a-service-fabric-cluster-on-azure-and-the-resources-it-uses"></a>Az Azure és az erőforrásokat, ezzel felhasznál egy Service Fabric-fürt törlése
A Service Fabric-fürt sok más Azure-erőforrások mellett magát fürterőforrás épül fel. A Service Fabric-fürtök teljes törléséhez ezért az összes őket alkotó erőforrást is törölni kell.
Két lehetőség közül választhat: vagy törölje az erőforráscsoportot, amely a fürt (amely törli a fürterőforrás és az erőforráscsoport összes erőforrását) vagy a kifejezetten a fürterőforrás törlése és a hozzá társított erőforrásokat (de nem egyéb erőforrások az erőforráscsoportban).

> [!NOTE]
> A fürterőforrás törlése **nem** törli az összes többi erőforrást, amely a Service Fabric-fürt áll.
> 
> 

## <a name="delete-the-entire-resource-group-rg-that-the-service-fabric-cluster-is-in"></a>A teljes erőforráscsoport (RG), amely a Service Fabric-fürt törlése
Ez a legegyszerűbb győződjön meg arról, hogy a fürthöz, beleértve az erőforráscsoport tartozó összes erőforrást törli az. Az erőforráscsoport PowerShell használatával törölheti vagy az Azure portálon keresztül. Ha az erőforráscsoport rendelkezik, amelyek nem kapcsolódnak a Service fabric-fürt, majd törölheti egy adott erőforráshoz.

### <a name="delete-the-resource-group-using-azure-powershell"></a>Az erőforráscsoportot, Azure PowerShell használatával
Az erőforráscsoport a következő Azure PowerShell-parancsmag futtatásával is törli. Győződjön meg arról, hogy Azure PowerShell 1.0 vagy nagyobb telepítve van a számítógépen. Ha nem ezt megelőzően, kövesse a témakörben ismertetett lépéseket [telepítése és konfigurálása az Azure PowerShell.](/powershell/azure/overview)

Nyisson meg egy PowerShell-ablakot, és a következő PS-parancsmagok futtatásához:

```powershell
Connect-AzureRmAccount

Remove-AzureRmResourceGroup -Name <name of ResouceGroup> -Force
```

Jelenít meg, ha nem használja, győződjön meg arról, hogy a törlés elérhetővé válik a *-Force* lehetőséget. A megerősítő az rg-n és a benne található összes erőforrást törlődnek.

### <a name="delete-a-resource-group-in-the-azure-portal"></a>Egy erőforráscsoportot az Azure-portálon
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Lépjen a törölni kívánt Service Fabric-fürtre.
3. Kattintson a fürt alapvető erőforrások lapon erőforráscsoport nevére.
4. Ekkor megjelenik a **erőforrás csoport Essentials** lap.
5. Kattintson a **Törlés** gombra.
6. Kövesse az utasításokat, hogy az erőforráscsoport törlésének befejezése lapon.

![Erőforrás-csoport törlése][ResourceGroupDelete]

## <a name="delete-the-cluster-resource-and-the-resources-it-uses-but-not-other-resources-in-the-resource-group"></a>A fürterőforrás és az erőforrásokat használ, de nem egyéb erőforrások az erőforráscsoportban törlése
Ha az erőforráscsoport csak a Service Fabric-fürt törli kapcsolódó erőforrásokat, majd célszerűbb a teljes csoport törléséhez. Ha azt szeretné, szelektív módon törli az erőforrást egy az egyhez által az erőforráscsoportban, majd kövesse az alábbi lépéseket.

Ha telepítette a fürthöz, a portál használatával, vagy használja a Service Fabric Resource Manager-sablonok egyikét a sablon gyűjteményből, majd a fürt által használt összes erőforrást címkével rendelkeznek a következő két címkékkel. Döntse el, hogy a törölni kívánt erőforrásokat használhatja őket.

***Címke #1:*** kulcs = fürtnév, érték = "a fürt neve"

***Címke #2:*** kulcsot resourceName, érték = = ServiceFabric

### <a name="delete-specific-resources-in-the-azure-portal"></a>Az Azure portálon meghatározott erőforrások törlése
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Lépjen a törölni kívánt Service Fabric-fürtre.
3. Ugrás a **összes beállítás** az essentials panel.
4. Kattintson a **címkék** alatt **erőforrás-kezelés** a beállítások panelen.
5. Kattintson a **címkék** a Címkék paneljén az adott címkével ellátott összes erőforrás listáját.
   
    ![Erőforráscímkék][ResourceTags]
6. Miután címkézett erőforrások listája, kattintson az összes erőforrás, és törölje őket.
   
    ![Címkézett erőforrások][TaggedResources]

### <a name="delete-the-resources-using-azure-powershell"></a>Az Azure PowerShell erőforrások törlése
A következő Azure PowerShell-parancsmag futtatásával törölheti egyesével-erőforrások. Győződjön meg arról, hogy Azure PowerShell 1.0 vagy nagyobb telepítve van a számítógépen. Ha nem ezt megelőzően, kövesse a témakörben ismertetett lépéseket [telepítése és konfigurálása az Azure PowerShell.](/powershell/azure/overview)

Nyisson meg egy PowerShell-ablakot, és a következő PS-parancsmagok futtatásához:

```powershell
Connect-AzureRmAccount
```
A törölni kívánt erőforrások mindegyikének futtassa a következő parancsfájlt:

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName "<name of the resource group>" -Force
```

A rendszer törli, futtassa a következő parancsfájlt:

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName "<name of the resource group>" -Force
```

## <a name="next-steps"></a>További lépések
Olvassa el a következő is megtudhat a fürt frissítése és particionálás szolgáltatások:

* [További tudnivalók a fürt frissítése](service-fabric-cluster-upgrade.md)
* [További tudnivalók a maximális méret állapotalapú szolgáltatások particionálás](service-fabric-concepts-partitioning.md)

<!--Image references-->
[ResourceGroupDelete]: ./media/service-fabric-cluster-delete/ResourceGroupDelete.PNG

[ResourceTags]: ./media/service-fabric-cluster-delete/ResourceTags.png

[TaggedResources]: ./media/service-fabric-cluster-delete/TaggedResources.PNG
