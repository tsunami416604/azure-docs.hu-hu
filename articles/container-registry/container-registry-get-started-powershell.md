---
title: Rövid útmutató – privát Docker-beállításjegyzék létrehozása az Azure-ban – PowerShell
description: Az útmutató azt ismerteti, hogyan hozhat létre egy privát Docker regisztrációs adatbázist az Azure-ban a PowerShell-lel.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: f99b4ee6dd11a109d1c563c84debc2157cb03337
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68309498"
---
# <a name="quickstart-create-a-private-container-registry-using-azure-powershell"></a>Gyors útmutató: Privát tároló beállításjegyzékének létrehozása Azure PowerShell használatával

Az Azure Container Registry egy felügyelt, privát Docker-tárolóregisztrációs adatbázis-szolgáltatás, amely a Docker-tárolók rendszerképeinek létrehozására, tárolására és kiszolgálására szolgál. Ebben a rövid útmutatóban megismerheti, hogyan hozhat létre Azure-tárolóregisztrációs adatbázist a PowerShell használatával. Ezután a Docker-parancsokkal leküldheti a tárolók rendszerképét a beállításjegyzékbe, és végül lekérdezheti és futtathatja a rendszerképet a beállításjegyzékből.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ehhez a rövid útmutatóhoz Azure PowerShell modul szükséges. Futtassa a `Get-Module -ListAvailable Az` parancsot a telepített verzió meghatározásához. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket.

A Dockert is telepítenie kell helyileg. A Docker csomagokat biztosít a [MacOS][docker-mac], a [Windows][docker-windows]és a [Linux][docker-linux] rendszerekhez.

Mivel az Azure Cloud Shell nem tartalmazza az összes szükséges Docker-összetevőt (a `dockerd`-démont), ehhez a rövid útmutatóhoz nem használható a Cloud Shell.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a [AzAccount][Connect-AzAccount] paranccsal, és kövesse a képernyőn megjelenő utasításokat.

```powershell
Connect-AzAccount
```

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Miután hitelesítette az Azure-t, hozzon létre egy erőforráscsoportot a [New-AzResourceGroup][New-AzResourceGroup]. Az erőforráscsoport egy olyan logikai tároló, amelyben üzembe helyezheti és kezelheti az Azure-erőforrásokat.

```powershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>Tárolóregisztrációs adatbázis létrehozása

Ezután hozzon létre egy tároló-beállításjegyzéket az új erőforráscsoporthoz a [New-AzContainerRegistry][New-AzContainerRegistry] paranccsal.

A beállításjegyzék nevének egyedinek kell lennie az Azure rendszerben, és 5–50 alfanumerikus karaktert kell tartalmaznia. Az alábbi példában egy „myContainerRegistry007” nevű adatbázis jön létre. Írja be a *myContainerRegistry007* kifejezést az alábbi parancsba, majd futtassa azt az adatbázis létrehozásához:

```powershell
$registry = New-AzContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

Ebben a rövid útmutatóban egy  alapszintű beállításjegyzéket hozunk létre, amely egy költséghatékony megoldás a fejlesztők számára a Azure Container Registry megismeréséhez. A rendelkezésre álló szolgáltatási szintek részletes ismertetését lásd: [Container Registry SKU][container-registry-skus]-i.

## <a name="log-in-to-registry"></a>Bejelentkezés a beállításjegyzékbe

A tárolórendszerképek mozgatásához először be kell jelentkeznie a beállításjegyzékbe. Éles környezetekben érdemes egyéni identitást vagy egyszerű szolgáltatásnevet használni a tároló beállításjegyzékének eléréséhez, de a rövid útmutató elvégzéséhez engedélyezze a rendszergazdai felhasználót a beállításjegyzékben a [Get-AzContainerRegistryCredential][Get-AzContainerRegistryCredential] paranccsal:

```powershell
$creds = Get-AzContainerRegistryCredential -Registry $registry
```

Ezután futtassa a [Docker][docker-login] -bejelentkezést a bejelentkezéshez:

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

A parancs a `Login Succeeded` üzenetet adja vissza, ha befejeződött.

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült az ebben a rövid útmutatóban létrehozott erőforrásokkal, a [Remove-AzResourceGroup][Remove-AzResourceGroup] paranccsal távolítsa el az erőforráscsoportot, a tároló-beállításjegyzéket és az ott tárolt tároló-lemezképeket:

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Azure Container Registryt Azure PowerShell, leküldte a tároló képét, és lehúzta és futtatta a rendszerképet a beállításjegyzékből. Folytassa a Azure Container Registry oktatóanyagokkal, és tekintse meg az ACR mélyebb megjelenését.

> [!div class="nextstepaction"]
> [Oktatóanyagok Azure Container Registry][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzAccount]: /powershell/module/az.accounts/connect-azaccount
[Get-AzContainerRegistryCredential]: /powershell/module/az.containerregistry/get-azcontainerregistrycredential
[Get-Module]: /powershell/module/microsoft.powershell.core/get-module
[New-AzContainerRegistry]: /powershell/module/az.containerregistry/New-AzContainerRegistry
[New-AzResourceGroup]: /powershell/module/az.resources/new-azresourcegroup
[Remove-AzResourceGroup]: /powershell/module/az.resources/remove-azresourcegroup
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
