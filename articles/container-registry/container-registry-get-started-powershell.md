---
title: "Rövid útmutató – Privát Docker regisztrációs adatbázis létrehozása az Azure-ban a PowerShell-lel"
description: "Az útmutató azt ismerteti, hogyan hozhat létre egy privát Docker regisztrációs adatbázist a PowerShell-lel."
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 02/12/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 80b5055dee35cd6efe62ee949c05aef386a3ba14
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2018
---
# <a name="create-an-azure-container-registry-using-powershell"></a>Azure Container Registry létrehozása a PowerShell-lel

Az Azure Container Registry egy felügyelt Docker-tárolóregisztrációs adatbázis-szolgáltatás, amely a privát Docker-tárolók rendszerképeinek tárolására szolgál. Ez az útmutató az Azure Container Registry-példányok PowerShell-lel való létrehozásának módját ismerteti.

Ehhez a rövid útmutatóhoz az Azure PowerShell-modul 3.6-os vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.

A Dockert is telepítenie kell helyileg. A Docker csomagokat biztosít, amelyekkel a Docker egyszerűen konfigurálható bármely [Mac][docker-mac], [Windows][docker-windows] vagy [Linux][docker-linux] rendszeren.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Login-AzureRmAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Login-AzureRmAccount
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

```bash
docker login $registry.LoginServer -u $creds.Username -p $creds.Password
```

A parancs a `Login Succeeded` üzenetet adja vissza, ha befejeződött. Előfordulhat, hogy megjelenik egy biztonsági figyelmeztetés, amely a `--password-stdin` paraméter használatát javasolja. Bár a paraméter használatát a cikk nem tárgyalja, javasoljuk, kövesse ezt az ajánlott eljárást. További információkért tekintse át a [docker login][docker-login] parancs leírását.

## <a name="push-image-to-acr"></a>Rendszerkép leküldése az ACR-be

Ahhoz, hogy rendszerképet tudjon küldeni egy Azure Container Registry tárolóregisztrációs adatbázisba, először szüksége van egy rendszerképre. Ha szükséges, futtassa a következő parancsot egy előre létrehozott rendszerkép lekéréshez a Docker Hubból.

```bash
docker pull microsoft/aci-helloworld
```

A rendszerképet fel kell címkézni az ACR bejelentkezési kiszolgálójának nevével. Az ACR-példány bejelentkezési kiszolgálójának teljes nevét a [Get-AzureRmContainerRegistry](/powershell/module/containerregistry/Get-AzureRmContainerRegistry) paranccsal kérheti le.

```powershell
Get-AzureRmContainerRegistry | Select Loginserver
```

Címkézze fel a rendszerképet a [docker tag][docker-tag] parancs használatával. Helyettesítse be az *acrLoginServer* helyére az ACR-példány bejelentkezési kiszolgálójának nevét.

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

Végül a [docker push][docker-push] paranccsal küldje le a rendszerképeket az ACR-példányba. Helyettesítse be az *acrLoginServer* helyére az ACR-példány bejelentkezési kiszolgálójának nevét.

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) paranccsal eltávolítható az erőforráscsoport, az ACR-példány és az összes tárolórendszerkép.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Azure-beli tároló-beállításjegyzéket az Azure CLI segítségével. Ha az Azure Container Registry és az Azure Container Instances együttes használatának módját szeretné megismerni, folytassa az Azure Container Instances oktatóanyagával.

> [!div class="nextstepaction"]
> [Az Azure Container Instances oktatóanyaga](../container-instances/container-instances-tutorial-prepare-app.md)

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/