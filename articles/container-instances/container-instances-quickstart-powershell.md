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
ms.openlocfilehash: b17cca7f0c00aba260b97b29345ff33156a50138
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53183947"
---
# <a name="quickstart-run-a-container-application-in-azure-container-instances-with-azure-powershell"></a>Gyors útmutató: Egy tároló-alkalmazás futtatása az Azure Container Instances szolgáltatásban az Azure PowerShell használatával

Az Azure Container Instances segítségével egyszerűen és gyorsan futtathat Docker-tárolókat az Azure-ban. Nem kell virtuális gépeket üzembe helyeznie vagy teljes körű tárolóvezérlési platformot használnia (amilyen például a Kubernetes). Ebben a rövid útmutatóban létre fog hozni az Azure Portalon egy Azure-beli Windows-tárolót, és egy teljes tartománynévvel (FQDN) elérhetővé fogja tenni az alkalmazását. Egyetlen üzembe helyezési parancsot kell végrehajtania, ami után néhány másodperccel már meg is nyithatja a futó alkalmazást:

![Az Azure Container Instances szolgáltatásban üzembe helyezett alkalmazás képe a böngészőben][qs-powershell-01]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 5.5-ös vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Azure Container Instancest – mint minden Azure-erőforrást – egy erőforráscsoportban kell üzembe helyezni. Az erőforráscsoportok lehetővé teszik az egymáshoz kapcsolódó Azure-erőforrások rendszerezését és kezelését.

Először hozzon létre egy erőforráscsoportot *myResourceGroup* néven az *eastus* helyen az alábbi [New-AzureRmResourceGroup][New-AzureRmResourceGroup] paranccsal:

 ```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Tároló létrehozása

Most, hogy már van egy erőforráscsoportja, futtathat egy tárolót az Azure-ban. Ahhoz, hogy tárolópéldányt hozzon létre az Azure PowerShell-lel, meg kell adnia az erőforráscsoport és a tárolópéldány nevét, valamint a Docker-tároló rendszerképét a [New-AzureRmContainerGroup][New-AzureRmContainerGroup] parancsmagnak. Közzéteheti a tárolókat az interneten egy vagy több port megnyitásával, egy DNS-névcímke megadásával, vagy mindkettővel. Ebben a rövid útmutatóban egy olyan DNS-névcímkével ellátott tárolót fog üzembe helyezni, amely egy Nano Serveren futó Internet Information Servicest (IIS) üzemeltet.

A tárolópéldány indításához futtassa az alábbi parancsot. A `-DnsNameLabel` értéknek egyedinek kell lennie abban az Azure-régióban, ahol a példányt létrehozza. Ha „DNS-névcímke nem érhető el” hibaüzenetet kap, próbálkozzon másik DNS-névcímkével.

 ```azurepowershell-interactive
New-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image microsoft/iis:nanoserver -OsType Windows -DnsNameLabel aci-demo-win
```

Néhány másodpercen belül meg kell kapnia az Azure válaszát. A tároló `ProvisioningState` beállításának értéke kezdetben **Creating** (Létrehozás), de néhány percen belül **Succeeded** (Sikerült) állapotra kell váltania. Az üzembe helyezés állapotát a [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] parancsmaggal ellenőrizheti:

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

Miután a tároló `ProvisioningState` értéke **Succeeded** (Sikerült) állapotba vált, nyissa meg az `Fqdn`-t a böngészőben. Ha az alábbihoz hasonló weboldal jelenik meg, gratulálunk! Sikeresen üzembe helyezett egy Docker-tárolóban futó alkalmazást az Azure-ban.

![Az Azure Container Instances használatával üzembe helyezett IIS képe a böngészőben][qs-powershell-01]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután végzett a tárolóval, a [Remove-AzureRmContainerGroup][Remove-AzureRmContainerGroup] parancsmag használatával távolíthatja el:

 ```azurepowershell-interactive
Remove-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy tárolópéldányt egy a nyilvános Docker Hub beállításjegyzékben található rendszerképből. Ha saját maga szeretne létrehozni és üzembe helyezni egy tárolórendszerképet egy Azure-beli privát tárolóregisztrációs adatbázisból, lépjen tovább az Azure Container Instances oktatóanyagára.

> [!div class="nextstepaction"]
> [Az Azure Container Instances oktatóanyaga](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/remove-azurermcontainergroup
