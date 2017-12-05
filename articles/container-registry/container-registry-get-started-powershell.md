---
title: "Gyors üzembe helyezés – titkos Docker beállításjegyzékbeli létrehozása az Azure PowerShell használatával"
description: "Ismerje meg, a titkos Docker-tároló beállításjegyzék létrehozása a PowerShell használatával gyorsan."
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: tysonn
ms.service: container-registry
ms.devlang: na
ms.topic: quicksart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: d165cc159f7da2c8e7f0be4f0fa7d353997ed5f9
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
---
# <a name="create-an-azure-container-registry-using-powershell"></a>Hozzon létre egy Azure tároló beállításjegyzék PowerShell használatával

Az Azure Container Registry egy felügyelt Docker-tárolóregisztrációs adatbázis-szolgáltatás, amely a privát Docker-tárolók rendszerképeinek tárolására szolgál. Ez az útmutató adatokat PowerShell használata Azure tároló beállításjegyzék létrehozását.

A gyors üzembe helyezés az Azure PowerShell 3,6 vagy újabb verziója szükséges. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.

Helyileg telepített Docker is rendelkeznie kell. A Docker csomagokat biztosít, amelyekkel a Docker egyszerűen konfigurálható bármely [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) vagy [Linux](https://docs.docker.com/engine/installation/#supported-platforms) rendszeren.

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

Hozzon létre egy ACR példány használatával a [New-AzureRMContainerRegistry](/powershell/module/containerregistry/New-AzureRMContainerRegistry) parancsot.

A beállításjegyzék neve **egyedinek kell lennie**. Az alábbi példában *myContainerRegistry007* szolgál. Ez egy egyedi értékre módosítani.

```powershell
$registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

## <a name="log-in-to-acr"></a>Jelentkezzen be ACR

A tárolórendszerképek mozgatásához először be kell jelentkeznie az ACR-példányba. Először is a [Get-AzureRmContainerRegistryCredential](/powershell/module/containerregistry/get-azurermcontainerregistrycredential) parancs használatával beszerezheti a rendszergazdai hitelesítő adatokat az ACR példány.

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

Ezután használhatja a [docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/) próbál bejelentkezni az ACR példány parancsot.

```bash
docker login $registry.LoginServer -u $creds.Username -p $creds.Password
```

A parancs a „Bejelentkezés sikeres” üzenetet adja vissza, ha befejeződött.

## <a name="push-image-to-acr"></a>ACR leküldéses lemezképet

Kép leküldése egy Azure-tárolóba beállításjegyzék, először egy lemezképet kell rendelkeznie. Ha szükséges, a következő parancsot egy előre létrehozott lemezképet lekérés a Docker központ.

```bash
docker pull microsoft/aci-helloworld
```

A kép kell megjelölni az ACR bejelentkezési kiszolgáló nevével. Futtassa a [Get-AzureRmContainerRegistry](/powershell/module/containerregistry/Get-AzureRmContainerRegistry) parancs sikeresen lefut a ACR példány bejelentkezési kiszolgáló nevét.

```powershell
Get-AzureRmContainerRegistry | Select Loginserver
```

A lemezkép használatával címkét a [docker címke](https://docs.docker.com/engine/reference/commandline/tag/) parancsot. Cserélje le *acrLoginServer* ACR példány bejelentkezési kiszolgáló nevével.

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

Végül [docker leküldéses](https://docs.docker.com/engine/reference/commandline/push/) a képek leküldéses ACR példányához. Cserélje le *acrLoginServer* ACR példány bejelentkezési kiszolgáló nevével.

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége, használhatja a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) parancs beírásával távolítsa el az erőforráscsoportot, ACR példány és az összes tároló-lemezképeket.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

A gyors üzembe helyezés, az az Azure-tároló beállításjegyzék az Azure parancssori felület segítségével létrehozott. Ha azt szeretné, Azure tároló beállításjegyzék használatát Azure tároló osztályt, továbbra is az Azure-tároló példányok oktatóanyag.

> [!div class="nextstepaction"]
> [Azure tároló példányok útmutató](../container-instances/container-instances-tutorial-prepare-app.md)