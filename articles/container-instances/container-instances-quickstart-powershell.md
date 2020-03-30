---
title: Rövid útmutató – Docker-tároló üzembe helyezése a tárolópéldányhoz – PowerShell
description: Ebben a rövid útmutatóban az Azure PowerShell segítségével gyorsan üzembe helyezhet egy tárolóba helyezett webalkalmazást, amely egy elszigetelt Azure-tárolópéldányban fut
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: quickstart
ms.date: 03/21/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: 9901b3f18973365dc9ceb8c85ff8587b6c2ea894
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "74533607"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-azure-powershell"></a>Rövid útmutató: Tárolópéldány üzembe helyezése az Azure PowerShell használatával az Azure-ban

Azure Container Instances használatával kiszolgáló nélküli Docker-tárolók at Azure-ban az egyszerűség és a sebesség használatával. Egy alkalmazást igény szerint egy tárolópéldányra telepíthet, ha nincs szüksége egy teljes tárolóvezelési platformra, például az Azure Kubernetes-szolgáltatásra.

Ebben a rövid útmutatóban az Azure PowerShell használatával telepítegy elszigetelt Windows-tárolót, és elérhetővé teheti az alkalmazást egy teljesen minősített tartománynévvel (FQDN). Néhány másodperccel egyetlen központi telepítési parancs végrehajtása után tallózhat a tárolóban futó alkalmazásközött:

![Az Azure Container Instances szolgáltatáshoz üzembe helyezett alkalmazás képe a böngészőben][qs-powershell-01]

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy helyileg telepíti és használja a PowerShellt, ez az oktatóanyag megköveteli az Azure PowerShell-modult. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure Container Instancest – mint minden Azure-erőforrást – egy erőforráscsoportban kell üzembe helyezni. Az erőforráscsoportok lehetővé teszik az egymáshoz kapcsolódó Azure-erőforrások rendszerezését és kezelését.

Először hozzon létre egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen a következő [New-AzResourceGroup][New-AzResourceGroup] paranccsal:

 ```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Tároló létrehozása

Most, hogy rendelkezik egy erőforráscsoporttal, futtathat egy tárolót az Azure-ban. Hozzon létre egy tárolópéldányt az Azure PowerShell használatával, adja meg az erőforráscsoport nevét, a tárolópéldány nevét és a Docker-tárolórendszerképet a [New-AzContainerGroup][New-AzContainerGroup] parancsmaghoz. Ebben a rövid útmutatóban `mcr.microsoft.com/windows/servercore/iis:nanoserver` a nyilvános lemezképet használja. Ez a lemezkép a Nano Server rendszerben futtatandó Microsoft Internet Information Services (IIS) csomagot csomagolja.

Közzéteheti a tárolókat az interneten egy vagy több port megnyitásával, egy DNS-névcímke megadásával, vagy mindkettővel. Ebben a rövid útmutatóban dns-névcímkével rendelkező tárolót telepít, hogy az IIS nyilvánosan elérhető.

A tárolópéldány indításához hajtson végre egy, az alábbihoz hasonló parancsot. Állítson `-DnsNameLabel` be egy olyan értéket, amely egyedi az Azure-régióban, ahol létrehozza a példányt. Ha „DNS-névcímke nem érhető el” hibaüzenetet kap, próbálkozzon másik DNS-névcímkével.

 ```azurepowershell-interactive
New-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image mcr.microsoft.com/windows/servercore/iis:nanoserver -OsType Windows -DnsNameLabel aci-demo-win
```

Néhány másodpercen belül meg kell kapnia az Azure válaszát. A tároló `ProvisioningState` beállításának értéke kezdetben **Creating** (Létrehozás), de néhány percen belül **Succeeded** (Sikerült) állapotra kell váltania. Ellenőrizze a telepítési állapotot a [Get-AzContainerGroup][Get-AzContainerGroup] parancsmaggal:

 ```azurepowershell-interactive
Get-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

A tároló üzembe helyezési állapota, teljes tartományneve (FQDN) és IP-címe megjelenik a parancsmag kimenetében:

```console
PS Azure:\> Get-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer


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

Miután a tároló `ProvisioningState` értéke **Succeeded** (Sikerült) állapotba vált, nyissa meg az `Fqdn`-t a böngészőben. Ha egy, az alábbihoz hasonló weboldal jelenik meg, gratulálunk! Sikeresen üzembe helyezett egy Docker-tárolóban futó alkalmazást az Azure-ban.

![Az Azure Container Instances használatával üzembe helyezett IIS képe a böngészőben][qs-powershell-01]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett a tárolóval, távolítsa el az [Remove-AzContainerGroup][Remove-AzContainerGroup] parancsmaggal:

 ```azurepowershell-interactive
Remove-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy tárolópéldányt egy a nyilvános Docker Hub beállításjegyzékben található rendszerképből. Ha szeretne létrehozni és üzembe helyezni egy tárolórendszerképet egy privát Azure-tárolóregisztrációs adatbázisból, lépjen tovább az Azure Container Instances oktatóanyagára.

> [!div class="nextstepaction"]
> [Az Azure Container Instances oktatóanyaga](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzResourceGroup]: /powershell/module/az.resources/new-Azresourcegroup
[New-AzContainerGroup]: /powershell/module/az.containerinstance/new-Azcontainergroup
[Get-AzContainerGroup]: /powershell/module/az.containerinstance/get-Azcontainergroup
[Remove-AzContainerGroup]: /powershell/module/az.containerinstance/remove-Azcontainergroup
