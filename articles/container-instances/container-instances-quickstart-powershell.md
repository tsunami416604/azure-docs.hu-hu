---
title: "Gyors üzembe helyezés – az első Azure tároló példányok tároló létrehozása a PowerShell használatával"
description: "Ismerkedés az Azure-tároló példányok hozzon létre egy Windows-tároló példányt a PowerShell használatával."
services: container-instances
documentationcenter: 
author: mmacy
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: ca10274fc6a23d7f5e7436dbaf72a6e7a918f275
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="create-your-first-container-in-azure-container-instances"></a>Az első tároló létrehozása az Azure Container Instances szolgáltatásban

Az Azure tároló példányok megkönnyíti a létrehozása és kezelése a Docker-tároló az Azure virtuális gépeket, vagy egy magasabb szintű szolgáltatást elfogadása nélkül.

A gyors üzembe helyezés a Windows-tároló létrehozása az Azure-ban, és tegye elérhetővé, az interneten, egy nyilvános IP-címmel. Ez a művelet egyetlen paranccsal hajtható végre. Belül egy kis ideig megjelenik a futó alkalmazások a böngésző:

![Az Azure Container Instances használatával üzembe helyezett alkalmazás képe a böngészőben][qs-powershell-01]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 3.6-os vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy Azure erőforráscsoport [New-AzureRmResourceGroup][New-AzureRmResourceGroup]. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

 ```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Tároló létrehozása

Létrehozhat egy tárolót, adja meg a nevét, egy Docker-lemezképet, és egy Azure erőforráscsoport a [New-AzureRmContainerGroup] [ New-AzureRmContainerGroup] parancsmag. Ha szeretné, közzéteheti a tárolót az interneten egy nyilvános IP-cím használatával. Ebben az esetben fut az Internet Information Services (IIS) Windows Nano Server tárolója fogjuk használni.

 ```azurepowershell-interactive
New-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image microsoft/iis:nanoserver -OsType Windows -IpAddressType Public
```

Néhány másodpercen belül egy a irányuló kérelemre adott válasz jelenik meg. Kezdetben a tárolóban van a **létrehozása** állapotát, de egy vagy két percen belül kell kezdődnie. Az állapotkezelő segítségével ellenőrizheti a [Get-AzureRmContainerGroup] [ Get-AzureRmContainerGroup] parancsmagot:

 ```azurepowershell-interactive
Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

A tároló üzembe helyezési állapota és IP-cím jelenik meg a parancsmag kimenete:

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

A tárolót egyszer **ProvisioningState** áthelyezése `Succeeded`, a böngésző, a megadott IP-cím használatával csatlakozni tud hozzá.

![Azure-tároló példányok böngészőben megtekintett használatával telepített IIS][qs-powershell-01]

## <a name="delete-the-container"></a>A tároló törlése

Amikor elkészült, a tárolóval, akkor is távolítsa el azt a [Remove-AzureRmContainerGroup] [ Remove-AzureRmContainerGroup] parancsmagot:

 ```azurepowershell-interactive
Remove-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>Következő lépések

A gyors üzembe helyezés elindított egy előre elkészített Windows tároló Azure tároló példányát. Ha azt szeretné, megpróbálhatja felépíteni a tároló saját magának, és telepítené őket az Azure-tároló példányok az Azure tároló beállításjegyzékkel, továbbra is az Azure-tároló példányok oktatóanyag.

> [!div class="nextstepaction"]
> [Azure tároló példányok útmutató](./container-instances-tutorial-prepare-app.md)

<!-- LINKS -->
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/remove-azurermcontainergroup

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png