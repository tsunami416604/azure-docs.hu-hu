---
title: Service Fabric-fürt törlése az Azure-ban | Microsoft Docs
description: Ez az oktatóanyag azt mutatja be, hogyan kell törölni egy Azure-ban üzemeltetett Service Fabric-fürtöt és annak összes erőforrását. Dönthet úgy, hogy a fürtöt tartalmazó teljes erőforráscsoportot törli, de az erőforrások külön-külön is törölhetők.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/26/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 5174bb696b456a66cfc0666ac5c475437e441d82
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54848852"
---
# <a name="tutorial-remove-a-service-fabric-cluster-running-in-azure"></a>Oktatóanyag: Távolítsa el az Azure-ban futó Service Fabric-fürt

Ez az oktatóanyag egy sorozat negyedik része, és az Azure-ban futó Service Fabric-fürtök törlésének módját ismerteti. A Service Fabric-fürtök teljes törléséhez a fürt által használt összes erőforrást is törölnie kell. Két lehetősége van: törölheti a fürtöt tartalmazó erőforráscsoportot (amivel a fürterőforrás mellett az erőforráscsoport összes többi erőforrását is törli), vagy törölheti az adott fürterőforrást és az ahhoz társított erőforrásokat (meghagyva az erőforráscsoport többi erőforrását).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Erőforráscsoport és a benne található összes erőforrás törlése
> * Kiválasztott erőforrások törlése az erőforráscsoportból

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Biztonságos [Windows-fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md) vagy [Linux-fürt](service-fabric-tutorial-create-vnet-and-linux-cluster.md) létrehozása az Azure-ban sablon használatával
> * [Fürt horizontális fel- és leskálázása](service-fabric-tutorial-scale-cluster.md)
> * [Fürt futtatókörnyezetének frissítése](service-fabric-tutorial-upgrade-cluster.md)
> * Fürt törlése

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Telepítse az [Azure PowerShell-modul 4.1-es vagy újabb verzióját](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps), vagy az [Azure CLI-t](/cli/azure/install-azure-cli).
* Biztonságos [Windows-fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md) vagy [Linux-fürt](service-fabric-tutorial-create-vnet-and-linux-cluster.md) létrehozása az Azure-ban

## <a name="delete-the-resource-group-containing-the-service-fabric-cluster"></a>A Service Fabric-fürtöt tartalmazó erőforráscsoport törlése
A fürt és az összes általa használt erőforrás törlésének legegyszerűbb módja az erőforráscsoport törlése.

Jelentkezzen be az Azure-ba, és válassza ki azon előfizetés azonosítóját, amelyikkel el szeretné távolítani a fürtöt.  Az [Azure Portalra](http://portal.azure.com) bejelentkezve keresheti meg az előfizetés azonosítóját. A fürt és az összes általa használt erőforrás a [Remove-AzureRMResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) parancsmaggal vagy az [az group delete](/cli/azure/group?view=azure-cli-latest) paranccsal törölhető.

```powershell
Connect-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
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
Connect-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Get-AzureRmResource -ResourceGroupName $groupname | ft
```

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="sfclustertutorialgroup"
az resource list --resource-group $ResourceGroupName
```

Minden törölni kívánt erőforráshoz futtassa a következő szkriptet:

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName $groupname -Force
```

```azurecli
az resource delete --name "<name of the Resource>" --resource-type "<Resource Type>" --resource-group $ResourceGroupName
```

A fürterőforrás törléséhez futtassa a következő szkriptet:

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName $groupname -Force
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
* Ismerje meg, hogyan kell [javítani a Windows](service-fabric-patch-orchestration-application.md) vagy [javítani a Linux operációs rendszert](service-fabric-patch-orchestration-application-linux.md) a fürtcsomópontokon.
* Ismerje meg, hogyan lehet összesíteni és összegyűjteni [Windows-fürtök](service-fabric-diagnostics-event-aggregation-wad.md) vagy [Linux-fürtök](service-fabric-diagnostics-event-aggregation-lad.md) eseményeit, és [beállítani a Log Analytics szolgáltatást](service-fabric-diagnostics-oms-setup.md) a fürtesemények monitorozására.
