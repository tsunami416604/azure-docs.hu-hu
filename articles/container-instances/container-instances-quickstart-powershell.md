---
title: Rövid útmutató – Az első Azure Container Instances-tároló létrehozása a PowerShell segítségével
description: Ebben a rövid útmutatóban az Azure PowerShell használatával helyezhet üzembe egy Windows-alapú tárolót az Azure Container Instances szolgáltatásban
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: quickstart
ms.date: 03/19/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 8670de00e3ab66ec4a5fa5c0c340e38182fd1ffd
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-your-first-container-in-azure-container-instances"></a>Rövid útmutató: Az első tároló létrehozása az Azure Container Instances szolgáltatásban

Az Azure Container Instances segítségével egyszerűen hozhat létre és felügyelhet Docker-tárolókat az Azure-ban anélkül, hogy virtuális gépeket kellene kiépítenie, vagy egy magasabb szolgáltatási szintre kellene váltania. Ebben a rövid útmutatóban létrehozhat egy Windows-alapú tárolót az Azure-ban, és közzéteheti az interneten egy teljes tartománynévvel (FQDN-nel). Ez a művelet egyetlen paranccsal hajtható végre. Néhány pillanat elteltével már láthatja is a böngészőjében a futó alkalmazást:

![Az Azure Container Instances használatával üzembe helyezett alkalmazás képe a böngészőben][qs-powershell-01]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 5.5-ös vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy Azure-erőforráscsoportot a [New-AzureRmResourceGroup][New-AzureRmResourceGroup] parancsmaggal. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

 ```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Tároló létrehozása

A tároló létrehozásához meg kell adnia egy nevet, egy Docker-rendszerképet és egy Azure-erőforráscsoportot a [New-AzureRmContainerGroup][New-AzureRmContainerGroup] parancsmaghoz. Ha szeretné, egy DNS-név címkével közzéteheti a tárolót az interneten.

Futtassa a következő parancsot az Internet Information Servicest (IIS) futtató Nano Server-tároló elindításához. A `-DnsNameLabel` értéknek egyedinek kell lennie abban az Azure-régióban, ahol a példányt létrehozza, így az egyediség biztosításához módosítania kell ezt az értéket.

 ```azurepowershell-interactive
New-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image microsoft/iis:nanoserver -OsType Windows -DnsNameLabel aci-demo-win
```

Néhány másodperc elteltével válasz érkezik a kérésre. A tároló kezdetben **Létrehozás** állapotban van, de néhány percen belül el kell indulnia. Az üzembe helyezés állapotát a [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] parancsmag használatával ellenőrizheti:

 ```azurepowershell-interactive
Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

A tároló üzembe helyezési állapota, teljes tartományneve (FQDN) és IP-címe megjelenik a parancsmag kimenetében:

```console
PS Azure:\> Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer


ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials :
RestartPolicy            : Always
IpAddress                : 52.226.19.87
DnsNameLabel             : aci-demo-win
Fqdn                     : aci-demo-win.eastus.azurecontainer.io
Ports                    : {80}
OsType                   : Windows
Volumes                  :
State                    : Pending
Events                   : {}
```

Miután a tároló **ProvisioningState** értéke `Succeeded` állapotba vált, lépjen a `Fqdn` elemére a böngészőben:

![Az Azure Container Instances használatával üzembe helyezett IIS képe a böngészőben][qs-powershell-01]

## <a name="delete-the-container"></a>A tároló törlése

Miután végzett a tárolóval, a [Remove-AzureRmContainerGroup][Remove-AzureRmContainerGroup] parancsmag használatával távolíthatja el:

 ```azurepowershell-interactive
Remove-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy előre elkészített Windows-tárolót indított az Azure Container Instances szolgáltatásban. Ha próbaképpen szeretne saját maga létrehozni és üzembe helyezni egy tárolót az Azure Container Instances szolgáltatásban az Azure Container Registry használatával, folytassa az Azure Container Instances oktatóanyagával.

> [!div class="nextstepaction"]
> [Az Azure Container Instances oktatóanyaga](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/remove-azurermcontainergroup
