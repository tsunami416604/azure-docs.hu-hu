---
title: Rövid útmutató – alkalmazás futtatása az Azure Container Instances szolgáltatásban – PowerShell
description: Ebben a rövid útmutatóban az Azure PowerShell használatával tároló üzembe helyezése a Docker Azure Container Instances szolgáltatásban az Azure PowerShell használatával
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: quickstart
ms.date: 10/02/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: b8cb84523288f45dfb719d69e4f7d227039598a9
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806912"
---
# <a name="quickstart-run-a-container-application-in-azure-container-instances-with-azure-powershell"></a>Gyors útmutató: Egy tároló-alkalmazás futtatása az Azure Container Instances szolgáltatásban az Azure PowerShell használatával

Az Azure Container Instances segítségével egyszerűen és gyorsan futtathat Docker-tárolókat az Azure-ban. Nem kell virtuális gépeket üzembe helyeznie vagy teljes körű tárolóvezérlési platformot használnia (amilyen például a Kubernetes). Ebben a rövid útmutatóban létre fog hozni az Azure Portalon egy Azure-beli Windows-tárolót, és egy teljes tartománynévvel (FQDN) elérhetővé fogja tenni az alkalmazását. Egyetlen üzembe helyezési parancsot kell végrehajtania, ami után néhány másodperccel már meg is nyithatja a futó alkalmazást:

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

Most, hogy már van egy erőforráscsoportja, futtathat egy tárolót az Azure-ban. Létrehoz egy tárolópéldányt, az Azure PowerShell-lel, adjon meg egy erőforráscsoport-név, tároló-példány nevét és a Docker-tároló rendszerképét az [New-AzContainerGroup] [ New-AzContainerGroup] parancsmagot. Ez a rövid a `microsoft/iis:nanoserver` Windows-lemezkép a nyilvános Docker Hub-beállításjegyzékből. Ez a rendszerkép csomagok Internet Information Services (IIS) a Nano Server futtatásához.

A tárolóit közzéteheti az interneten. Ehhez adjon meg egy vagy több megnyitni kívánt portot, egy DNS-névcímkét vagy mindkettőt. Ebben a rövid útmutatóban üzembe helyezi egy-egy DNS-névcímke tárolóban úgy, hogy az IIS nyilvánosan elérhető.

A tárolópéldány indításához futtassa az alábbi parancsot. A `-DnsNameLabel` értéknek egyedinek kell lennie abban az Azure-régióban, ahol a példányt létrehozza. Ha „DNS-névcímke nem érhető el” hibaüzenetet kap, próbálkozzon másik DNS-névcímkével.

 ```azurepowershell-interactive
New-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image microsoft/iis:nanoserver -OsType Windows -DnsNameLabel aci-demo-win
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
