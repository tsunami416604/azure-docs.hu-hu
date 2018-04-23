---
title: Rövid útmutató – Privát Docker regisztrációs adatbázis létrehozása az Azure-ban a PowerShell-lel
description: Az útmutató azt ismerteti, hogyan hozhat létre egy privát Docker regisztrációs adatbázist a PowerShell-lel.
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 03/03/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: ae21fc7ab016071d075324bf813243cef58dcd04
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="quickstart-create-an-azure-container-registry-using-powershell"></a>Rövid útmutató: Azure Container Registry létrehozása a PowerShell-lel

Az Azure Container Registry egy felügyelt Docker-tárolóregisztrációs adatbázis-szolgáltatás, amely a privát Docker-tárolók rendszerképeinek tárolására szolgál. Ez az útmutató a következőket ismerteti: Azure Container Registry-példány létrehozása a PowerShell használatával, tárolórendszerkép továbbítása a regisztrációs adatbázisba, végül a tároló üzembe helyezése a regisztrációs adatbázisból az Azure Container Instances (ACI) szolgáltatásban.

Ehhez a rövid útmutatóhoz az Azure PowerShell-modul 3.6-os vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.

A Dockert is telepítenie kell helyileg. A Docker csomagokat biztosít, amelyekkel a Docker egyszerűen konfigurálható bármely [Mac][docker-mac], [Windows][docker-windows] vagy [Linux][docker-linux] rendszeren.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Connect-AzureRmAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Connect-AzureRmAccount
```

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy Azure-erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) parancsmaggal. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása

Hozzon létre egy ACR-példányt a [New-AzureRMContainerRegistry](/powershell/module/containerregistry/New-AzureRMContainerRegistry) paranccsal.

A beállításjegyzék nevének egyedinek kell lennie az Azure rendszerben, és 5–50 alfanumerikus karaktert kell tartalmaznia. Az alábbi példában a *myContainerRegistry007* nevet használjuk. Ezt cserélje le egy egyedi értékre.

```powershell
$registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

## <a name="log-in-to-acr"></a>Jelentkezzen be az ACR-be

A tárolórendszerképek mozgatásához először be kell jelentkeznie az ACR-példányba. Először a [Get-AzureRmContainerRegistryCredential](/powershell/module/containerregistry/get-azurermcontainerregistrycredential) paranccsal kérje le az ACR-példány rendszergazdai hitelesítő adatait.

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

Ezután a [docker login][docker-login] paranccsal jelentkezzen be az ACR-példányba.

```powershell
docker login $registry.LoginServer -u $creds.Username -p $creds.Password
```

A parancs a `Login Succeeded` üzenetet adja vissza, ha befejeződött. Előfordulhat, hogy megjelenik egy biztonsági figyelmeztetés, amely a `--password-stdin` paraméter használatát javasolja. Bár a paraméter használatát a cikk nem tárgyalja, javasoljuk, kövesse ezt az ajánlott eljárást. További információkért tekintse át a [docker login][docker-login] parancs leírását.

## <a name="push-image-to-acr"></a>Rendszerkép leküldése az ACR-be

Ahhoz, hogy rendszerképet tudjon küldeni egy Azure Container Registry tárolóregisztrációs adatbázisba, először szüksége van egy rendszerképre. Ha szükséges, futtassa a következő parancsot egy előre létrehozott rendszerkép lekéréshez a Docker Hubból.

```powershell
docker pull microsoft/aci-helloworld
```

A rendszerképet fel kell címkézni az ACR bejelentkezési kiszolgálójának nevével. Ehhez használja a [docker tag][docker-tag] parancsot.

```powershell
$image = $registry.LoginServer + "/aci-helloworld:v1"
docker tag microsoft/aci-helloworld $image
```

Végül a [docker push][docker-push] paranccsal továbbítsa a rendszerképet az ACR-be.

```powershell
docker push $image
```

## <a name="deploy-image-to-aci"></a>Rendszerkép üzembe helyezése az ACI szolgáltatásban
A rendszerképnek az Azure Container Instances (ACI) szolgáltatásban tárolópéldányként való üzembe helyezéséhez először konvertálja a regisztrációs adatbázis hitelesítő adatait PSCredential adattá.

```powershell
$secpasswd = ConvertTo-SecureString $creds.Password -AsPlainText -Force
$pscred = New-Object System.Management.Automation.PSCredential($creds.Username, $secpasswd)
```

A tárolórendszerképnek a tárolóregisztrációs adatbázisból 1 processzormaggal és 1 GB memóriával történő üzembe helyezéséhez futtassa az alábbi parancsot:

```powershell
New-AzureRmContainerGroup -ResourceGroup myResourceGroup -Name mycontainer -Image $image -Cpu 1 -MemoryInGB 1 -IpAddressType public -Port 80 -RegistryCredential $pscred
```

Az Azure Resource Manager kezdeti válaszát kell megkapnia a tároló részleteivel. A tároló állapotának monitorozásához és annak ellenőrzéséhez, hogy mikor fut, ismételje meg a [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] parancsot. Ez kevesebb mint egy percet vesz igénybe.

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
```

Példa a kimenetre: `Succeeded`

## <a name="view-the-application"></a>Az alkalmazás megtekintése
Ha az üzembe helyezés az ACI szolgáltatásban sikeresen megtörtént, a [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] paranccsal kérje le a tároló teljes tartománynevét:

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).IpAddress
```

Példa a kimenetre: `"13.72.74.222"`

A futó alkalmazás megtekintéséhez nyissa meg a nyilvános IP-címet kedvenc böngészőjében. A következőhöz hasonló eredményt kell kapnia:

![A Hello World alkalmazás a böngészőben][qs-portal-15]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [Remove-AzureRmResourceGroup][Remove-AzureRmResourceGroup] paranccsal eltávolítható az erőforráscsoport, az Azure Container Registry és az Azure Container Instances.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Azure Container Registry-példányt az Azure CLI segítségével, és futtatta annak egy példányát az Azure Container Instances szolgáltatásban. Folytassa az Azure Container Instances oktatóanyagával, amelyben alaposabban megismerheti az ACI szolgáltatást.

> [!div class="nextstepaction"]
> [Az Azure Container Instances oktatóanyaga](../container-instances/container-instances-tutorial-prepare-app.md)

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmResourceGroup]: /powershell/module/azurerm.resources/remove-azurermresourcegroup

<!-- IMAGES> -->
[qs-portal-15]: ./media/container-registry-get-started-portal/qs-portal-15.png
