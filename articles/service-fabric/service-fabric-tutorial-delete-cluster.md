---
title: Service Fabric-fürt törlése az Azure-ban | Microsoft Docs
description: Ez az oktatóanyag azt mutatja be, hogyan kell törölni egy Azure-ban üzemeltetett Service Fabric-fürtöt és annak összes erőforrását. Dönthet úgy, hogy a fürtöt tartalmazó teljes erőforráscsoportot törli, de az erőforrások külön-külön is törölhetők.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/26/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 38e776a5398ea5217a0d7e385d6ebb45d2199d51
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59044607"
---
# <a name="tutorial-remove-a-service-fabric-cluster-running-in-azure"></a>Oktatóanyag: Távolítsa el az Azure-ban futó Service Fabric-fürt

Ez az oktatóanyag része, öt egy sorozat harmadik része, és bemutatja, hogyan lehet törölni az Azure-ban futó Service Fabric-fürt. A Service Fabric-fürtök teljes törléséhez a fürt által használt összes erőforrást is törölnie kell. Két lehetősége van: törölheti a fürtöt tartalmazó erőforráscsoportot (amivel a fürterőforrás mellett az erőforráscsoport összes többi erőforrását is törli), vagy törölheti az adott fürterőforrást és az ahhoz társított erőforrásokat (meghagyva az erőforráscsoport többi erőforrását).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Erőforráscsoport és a benne található összes erőforrás törlése
> * Kiválasztott erőforrások törlése az erőforráscsoportból

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Hozzon létre egy biztonságos [Windows-fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md) az Azure-ban sablon használatával
> * [-Fürt monitorozása](service-fabric-tutorial-monitor-cluster.md)
> * [Fürt horizontális fel- és leskálázása](service-fabric-tutorial-scale-cluster.md)
> * [Fürt futtatókörnyezetének frissítése](service-fabric-tutorial-upgrade-cluster.md)
> * Fürt törlése


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Telepítés [az Azure Powershell](https://docs.microsoft.com/powershell/azure//install-Az-ps) vagy [az Azure CLI](/cli/azure/install-azure-cli).
* Hozzon létre egy biztonságos [Windows-fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md) az Azure-ban

## <a name="delete-the-resource-group-containing-the-service-fabric-cluster"></a>A Service Fabric-fürtöt tartalmazó erőforráscsoport törlése
A fürt és az összes általa használt erőforrás törlésének legegyszerűbb módja az erőforráscsoport törlése.

Jelentkezzen be az Azure-ba, és válassza ki az előfizetés-azonosító, amellyel el szeretné távolítani a fürtöt.  Az [Azure Portalra](https://portal.azure.com) bejelentkezve keresheti meg az előfizetés azonosítóját. Törölje az erőforráscsoportot és a fürt erőforrásai használatával az [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsmag vagy [az csoport törlése](/cli/azure/group?view=azure-cli-latest) parancsot.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Remove-AzResourceGroup -Name $groupname -Force
```

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="sfclustertutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="selectively-delete-the-cluster-resource-and-the-associated-resources"></a>A fürterőforrás és a társított erőforrások célzott törlése
Ha az erőforráscsoport csak a törölni kívánt Service Fabric-fürthöz kapcsolódó erőforrásokat tartalmazza, egyszerűbb az egész erőforráscsoportot törölni. Ha csak az erőforráscsoport egyes erőforrásait szeretné törölni, és meg szeretné tartani a fürthöz nem kapcsolódó erőforrásokat, kövesse az alábbi lépéseket.

Listázza az erőforráscsoport erőforrásait:

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Get-AzResource -ResourceGroupName $groupname | ft
```

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="sfclustertutorialgroup"
az resource list --resource-group $ResourceGroupName
```

Minden törölni kívánt erőforráshoz futtassa a következő szkriptet:

```powershell
Remove-AzResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName $groupname -Force
```

```azurecli
az resource delete --name "<name of the Resource>" --resource-type "<Resource Type>" --resource-group $ResourceGroupName
```

A fürterőforrás törléséhez futtassa a következő szkriptet:

```powershell
Remove-AzResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName $groupname -Force
```

```azurecli
az resource delete --name "<name of the Resource>" --resource-type "Microsoft.ServiceFabric/clusters" --resource-group $ResourceGroupName
```

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Erőforráscsoport és a benne található összes erőforrás törlése
> * Kiválasztott erőforrások törlése az erőforráscsoportból

Most, hogy elvégezte az oktatóanyagot, tekintse át a következőket:
* Ismerje meg, hogyan vizsgálhat meg és kezelhet egy Service Fabric-fürtöt a [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) használatával.
* Ismerje meg, hogyan [a Windows operációs rendszer](service-fabric-patch-orchestration-application.md) a fürtcsomópontokat.
* Ismerje meg, hogyan összesítheti és eseményeket gyűjtő [Windows-fürtök](service-fabric-diagnostics-event-aggregation-wad.md) és [Log Analytics beállítása](service-fabric-diagnostics-oms-setup.md) fürthöz kapcsolódó események figyelésére.
MS-setup.md) fürthöz kapcsolódó események figyelésére.
