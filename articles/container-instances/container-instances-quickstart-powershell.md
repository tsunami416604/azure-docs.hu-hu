---
title: Rövid útmutató – Docker-tároló üzembe helyezése az Azure Container Instances – PowerShell
description: Ebben a rövid útmutatóban az Azure PowerShell használatával gyorsan üzembe helyezhet egy tárolóalapú webalkalmazást, amely egy elkülönített Azure-tárolópéldányon
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 8c50a3069ea8b1303e45c571425a6f4c9b4c0d5b
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368188"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-azure-powershell"></a>Gyors útmutató: Üzembe helyezéséhez az Azure-ban az Azure PowerShell-lel

Azure Container Instances használatával kiszolgáló nélküli Docker-tárolókat futtathat az Azure-ban egyszerű és gyors. Egy tároló példány igény szerinti-alkalmazás üzembe helyezése, ha már nincs szükség a teljes tárolót vezénylési platformot hasonlóan az Azure Kubernetes Service-ben.

Ebben a rövid útmutatóban az Azure PowerShell használatával egy elkülönített Windows-tároló üzembe helyezése és elérhetővé teheti az alkalmazás a teljesen minősített tartománynevét (FQDN). Néhány másodpercet, egy egyetlen központi telepítési parancs végrehajtása után megnyithatja az a tárolóban futó alkalmazásnak:

![Az Azure Container Instances szolgáltatásban üzembe helyezett alkalmazás képe a böngészőben][qs-powershell-01]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Ha helyi telepítése és használata a PowerShell, az oktatóanyaghoz az Azure PowerShell modult. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Azure Container Instancest – mint minden Azure-erőforrást – egy erőforráscsoportban kell üzembe helyezni. Az erőforráscsoportok lehetővé teszik az egymáshoz kapcsolódó Azure-erőforrások rendszerezését és kezelését.

Először is hozzon létre egy erőforráscsoportot *myResourceGroup* a a *eastus* helyet az alábbi [New-AzResourceGroup] [ New-AzResourceGroup] a parancs:

 ```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Tároló létrehozása

Most, hogy már van egy erőforráscsoportja, futtathat egy tárolót az Azure-ban. Létrehoz egy tárolópéldányt, az Azure PowerShell-lel, adjon meg egy erőforráscsoport-név, tároló-példány nevét és a Docker-tároló rendszerképét az [New-AzContainerGroup] [ New-AzContainerGroup] parancsmagot. Ebben a rövid útmutatóban használhatja a nyilvános `mcr.microsoft.com/windows/servercore/iis:nanoserver` kép. Ez a rendszerkép a Microsoft Internet Information Services (IIS) futtathat a Nano Server csomagok.

A tárolóit közzéteheti az interneten. Ehhez adjon meg egy vagy több megnyitni kívánt portot, egy DNS-névcímkét vagy mindkettőt. Ebben a rövid útmutatóban üzembe helyezi egy-egy DNS-névcímke tárolóban úgy, hogy az IIS nyilvánosan elérhető.

A tárolópéldány indításához az alábbihoz hasonló parancs végrehajtása. Állítsa be a `-DnsNameLabel` érték, amely egyedi az Azure-régióban, ahol létrehozhatja a példányt. Ha „A DNS-névcímke nem érhető el” hibaüzenetet kap, próbálkozzon másik DNS-névcímkével.

 ```azurepowershell-interactive
New-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image mcr.microsoft.com/windows/servercore/iis:nanoserver -OsType Windows -DnsNameLabel aci-demo-win
```

Néhány másodpercen belül meg kell kapnia az Azure válaszát. A tároló `ProvisioningState` beállításának értéke kezdetben **Creating** (Létrehozás), de néhány percen belül **Succeeded** (Sikerült) állapotra kell váltania. Az üzembe helyezési állapot ellenőrzése a [Get-AzContainerGroup] [ Get-AzContainerGroup] parancsmagot:

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

Miután a tároló `ProvisioningState` értéke **Succeeded** (Sikerült) állapotba vált, nyissa meg az `Fqdn`-t a böngészőben. Ha az alábbihoz hasonló weboldal jelenik meg, gratulálunk! Sikeresen üzembe helyezett egy Docker-tárolóban futó alkalmazást az Azure-ban.

![Az Azure Container Instances használatával üzembe helyezett IIS képe a böngészőben][qs-powershell-01]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Amikor végzett a tárolóval, távolítsa el azt a [Remove-AzContainerGroup] [ Remove-AzContainerGroup] parancsmagot:

 ```azurepowershell-interactive
Remove-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy tárolópéldányt egy a nyilvános Docker Hub beállításjegyzékben található rendszerképből. Ha saját maga szeretne létrehozni és üzembe helyezni egy tárolórendszerképet egy Azure-beli privát tárolóregisztrációs adatbázisból, lépjen tovább az Azure Container Instances oktatóanyagára.

> [!div class="nextstepaction"]
> [Az Azure Container Instances oktatóanyaga](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzResourceGroup]: /powershell/module/az.resources/new-Azresourcegroup
[New-AzContainerGroup]: /powershell/module/az.containerinstance/new-Azcontainergroup
[Get-AzContainerGroup]: /powershell/module/az.containerinstance/get-Azcontainergroup
[Remove-AzContainerGroup]: /powershell/module/az.containerinstance/remove-Azcontainergroup
