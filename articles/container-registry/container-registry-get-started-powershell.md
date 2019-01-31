---
title: Rövid útmutató – privát Docker-tárolójegyzék létrehozása az Azure-ban – PowerShell
description: Az útmutató azt ismerteti, hogyan hozhat létre egy privát Docker regisztrációs adatbázist az Azure-ban a PowerShell-lel.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: b8ff8e671d51a148177e66b30225dd7536a48028
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55299743"
---
# <a name="quickstart-create-a-private-container-registry-using-azure-powershell"></a>Gyors útmutató: Hozzon létre egy privát tárolójegyzékben Azure PowerShell-lel

Az Azure Container Registry egy felügyelt, privát Docker-tárolóregisztrációs adatbázis-szolgáltatás, amely a Docker-tárolók rendszerképeinek létrehozására, tárolására és kiszolgálására szolgál. Ebben a rövid útmutatóban megismerheti, hogyan hozhat létre Azure-tárolóregisztrációs adatbázist a PowerShell használatával. Ezután a Docker-parancsok segítségével továbbít egy rendszerképet a regisztrációs adatbázisba, és végül kérje le, és futtassa a rendszerképet a regisztrációs adatbázisból.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz az Azure PowerShell-modul 5.7.0-s vagy újabb verziójára lesz szükség. Futtassa a `Get-Module -ListAvailable AzureRM` parancsot a telepített verzió meghatározásához. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/azurerm/install-azurerm-ps) ismertető cikket.

A Dockert is telepítenie kell helyileg. A Docker [macOS][docker-mac], [Windows][docker-windows] és [Linux][docker-linux] operációs rendszerekhez biztosít csomagokat.

Mivel az Azure Cloud Shell nem tartalmazza az összes szükséges Docker-összetevőt (a `dockerd`-démont), ehhez a rövid útmutatóhoz nem használható a Cloud Shell.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a [Connect-AzureRmAccount][Connect-AzureRmAccount] paranccsal, majd kövesse a képernyőn megjelenő utasításokat.

```powershell
Connect-AzureRmAccount
```

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-hitelesítés után hozzon létre egy erőforráscsoportot a [New-AzureRmResourceGroup][New-AzureRmResourceGroup] paranccsal. Az erőforráscsoport egy olyan logikai tároló, amelyben üzembe helyezheti és kezelheti az Azure-erőforrásokat.

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>Tárolóregisztrációs adatbázis létrehozása

Ezt követően hozzon létre egy tárolóregisztrációs adatbázist az új erőforráscsoportban a [New-AzureRMContainerRegistry][New-AzureRMContainerRegistry] paranccsal.

A beállításjegyzék nevének egyedinek kell lennie az Azure rendszerben, és 5–50 alfanumerikus karaktert kell tartalmaznia. Az alábbi példában egy „myContainerRegistry007” nevű adatbázis jön létre. Írja be a *myContainerRegistry007* kifejezést az alábbi parancsba, majd futtassa azt az adatbázis létrehozásához:

```powershell
$registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

Ebben a rövid útmutatóban létrehozott egy *alapszintű* beállításjegyzékbe, amely költségek optimalizált megoldás megismerése az Azure Container Registry-fejlesztőknek. További információ az elérhető szolgáltatáscsomagban: [tároló-beállításjegyzék Termékváltozatai][container-registry-skus].

## <a name="log-in-to-registry"></a>Bejelentkezés a beállításjegyzékbe

A tárolórendszerképek mozgatásához először be kell jelentkeznie a beállításjegyzékbe. Éles forgatókönyvekben, érdemes egyéni identitás vagy egyszerű szolgáltatás használata a tárolóregisztrációs adatbázis eléréséhez, de, ez a rövid útmutató rövid, hogy engedélyezze a rendszergazdai felhasználót a regisztrációs adatbázisban az [Get-AzureRmContainerRegistryCredential] [ Get-AzureRmContainerRegistryCredential] parancsot:

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

Ezután a bejelentkezéshez futtassa a [docker login][docker-login] parancsot:

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

A parancs a `Login Succeeded` üzenetet adja vissza, ha befejeződött.

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután elkészült az erőforrásokkal való munkához, a rövid útmutatóban létrehozott, használja a [Remove-AzureRmResourceGroup] [ Remove-AzureRmResourceGroup] paranccsal eltávolítható az erőforráscsoport, a tároló-beállításjegyzék és a tároló ott tárolt képeket:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Azure Container Registry az Azure PowerShell használatával, leküldtünk bele egy tárolórendszerképet, és kéri le, és futtatta a rendszerképet a beállításjegyzékből. Továbbra is az Azure Container Registry oktatóanyagok az ACR alaposabban.

> [!div class="nextstepaction"]
> [Azure Container Registry-oktatóanyagok][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzureRmAccount]: /powershell/module/azurerm.profile/connect-azurermaccount
[Get-AzureRmContainerRegistryCredential]: /powershell/module/azurerm.containerregistry/get-azurermcontainerregistrycredential
[Get-Module]: /powershell/module/microsoft.powershell.core/get-module
[New-AzureRMContainerRegistry]: /powershell/module/azurerm.containerregistry/New-AzureRMContainerRegistry
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[Remove-AzureRmResourceGroup]: /powershell/module/azurerm.resources/remove-azurermresourcegroup
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md