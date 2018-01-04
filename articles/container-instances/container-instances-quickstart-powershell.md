---
title: "Rövid útmutató – Az első Azure Container Instances-tároló létrehozása a PowerShell segítségével"
description: "Ismerje meg az Azure Container Instances szolgáltatást egy Windows-tárolópéldányt létrehozásával a PowerShell segítségével."
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: quickstart
ms.date: 01/02/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 3d8516c0f3f6cc8185b16f4cdabcc391e31cc605
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2018
---
# <a name="create-your-first-container-in-azure-container-instances"></a>Az első tároló létrehozása az Azure Container Instances szolgáltatásban

Az Azure Container Instances segítségével egyszerűen hozhat létre és felügyelhet Docker-tárolókat az Azure-ban anélkül, hogy virtuális gépeket kellene kiépítenie, vagy egy magasabb szolgáltatási szintre kellene váltania.

Ebben a rövid útmutatóban létrehozhat egy Windows-tárolót az Azure-ban, és közzéteheti az interneten egy nyilvános IP-címen keresztül. Ez a művelet egyetlen paranccsal hajtható végre. Néhány pillanat elteltével már láthatja is a böngészőben futó alkalmazást.

![Az Azure Container Instances használatával üzembe helyezett alkalmazás képe a böngészőben][qs-powershell-01]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 3.6-os vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy Azure-erőforráscsoportot a [New-AzureRmResourceGroup][New-AzureRmResourceGroup] parancsmaggal. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

 ```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Tároló létrehozása

A tároló létrehozásához meg kell adnia egy nevet, egy Docker-rendszerképet és egy Azure-erőforráscsoportot a [New-AzureRmContainerGroup][New-AzureRmContainerGroup] parancsmaghoz. Ha szeretné, közzéteheti a tárolót az interneten egy nyilvános IP-cím használatával. Ebben az esetben egy Internet Information Services (IIS) szolgáltatást futtató Windows Nano Server-tárolót használunk.

 ```azurepowershell-interactive
New-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image microsoft/iis:nanoserver -OsType Windows -IpAddressType Public
```

Néhány másodperc elteltével válasz érkezik a kérésre. A tároló kezdetben **Létrehozás** állapotban van, de néhány percen belül el kell indulnia. Az állapotot a [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] parancsmag használatával ellenőrizheti:

 ```azurepowershell-interactive
Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

A tároló üzembe helyezési állapota és IP-címe megjelenik a parancsmag kimenetében:

```
ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/12345678-1234-1234-1234-12345678abcd/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials :
RestartPolicy            :
IpAddress                : 40.71.248.73
Ports                    : {80}
OsType                   : Windows
Volumes                  :
```

Miután a tároló **ProvisioningState** értéke `Succeeded` állapotba vált, elérhetővé válik a böngészőben a megadott IP-címen.

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