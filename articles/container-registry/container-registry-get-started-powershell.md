---
title: Rövid útmutató - Rendszerleíró adatbázis létrehozása - Powershell
description: Gyorsan megtudhatja, hogyan hozhat létre privát Docker-rendszerleíró adatbázist az Azure Container Registry szolgáltatásban a PowerShell segítségével
ms.topic: quickstart
ms.date: 01/22/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: 872b2a29444e5278db34ce44741e2ca90d885702
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "74456371"
---
# <a name="quickstart-create-a-private-container-registry-using-azure-powershell"></a>Rövid útmutató: Hozzon létre egy privát tároló beállításjegyzéket az Azure PowerShell használatával

Az Azure Container Registry egy felügyelt, privát Docker-tárolóregisztrációs adatbázis-szolgáltatás, amely a Docker-tárolók rendszerképeinek létrehozására, tárolására és kiszolgálására szolgál. Ebben a rövid útmutatóban megismerheti, hogyan hozhat létre Azure-tárolóregisztrációs adatbázist a PowerShell használatával. Ezután a Docker-parancsok használatával leküldéses egy tárolórendszerképet a rendszerleíró adatbázisba, és végül lekéri és futtatja a rendszerképet a rendszerleíró adatbázisból.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ehhez a rövid útmutatóhoz Azure PowerShell-modul szükséges. Futtassa a `Get-Module -ListAvailable Az` parancsot a telepített verzió meghatározásához. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket.

A Dockert is telepítenie kell helyileg. A Docker [macOS][docker-mac], [Windows][docker-windows] és [Linux][docker-linux] operációs rendszerekhez biztosít csomagokat.

Mivel az Azure Cloud Shell nem tartalmazza az összes szükséges Docker-összetevőt (a `dockerd`-démont), ehhez a rövid útmutatóhoz nem használható a Cloud Shell.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be Azure-előfizetésébe a [Connect-AzAccount][Connect-AzAccount] paranccsal, és kövesse a képernyőn megjelenő utasításokat.

```powershell
Connect-AzAccount
```

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Miután hitelesítette magát az Azure-ral, hozzon létre egy erőforráscsoportot a [New-AzResourceGroup][New-AzResourceGroup]segítségével. Az erőforráscsoport egy olyan logikai tároló, amelyben üzembe helyezheti és kezelheti az Azure-erőforrásokat.

```powershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>Tárolóregisztrációs adatbázis létrehozása

Ezután hozzon létre egy tárolóbeállításjegyzéket az új erőforráscsoportban a [New-AzContainerRegistry][New-AzContainerRegistry] paranccsal.

A beállításjegyzék nevének egyedinek kell lennie az Azure rendszerben, és 5–50 alfanumerikus karaktert kell tartalmaznia. Az alábbi példában egy „myContainerRegistry007” nevű adatbázis jön létre. Írja be a *myContainerRegistry007* kifejezést az alábbi parancsba, majd futtassa azt az adatbázis létrehozásához:

```powershell
$registry = New-AzContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

Ebben a rövid útmutatóban hozzon létre egy *alapszintű* beállításjegyzéket, amely egy költségoptimalizált lehetőség a fejlesztők számára, hogy megismerjék az Azure Container Registry-t. Az elérhető szolgáltatásszintekről a [Container registry ska-k][container-registry-skus]ban talál további részleteket.

## <a name="log-in-to-registry"></a>Bejelentkezés a beállításjegyzékbe

A tárolórendszerképek mozgatásához először be kell jelentkeznie a beállításjegyzékbe. Éles környezetben egyéni identitást vagy egyszerű szolgáltatást kell használnia a tárolóbeállítás-bejegyzések eléréséhez, de a rövid útmutató rövid állapotának megtartásához engedélyezze a rendszergazda felhasználót a rendszerleíró adatbázisban a [Get-AzContainerRegistryCredential][Get-AzContainerRegistryCredential] paranccsal:

```powershell
$creds = Get-AzContainerRegistryCredential -Registry $registry
```

Ezután a bejelentkezéshez futtassa a [docker login][docker-login] parancsot:

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

A parancs a `Login Succeeded` üzenetet adja vissza, ha befejeződött.

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután végzett a rövid útmutatóban létrehozott erőforrásokkal, az [Eltávolítás-AzResourceGroup][Remove-AzResourceGroup] paranccsal távolítsa el az erőforráscsoportot, a tároló-beállításjegyzéket és az ott tárolt tárolólemezképeket:

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Azure Container Registry az Azure PowerShell, leadott egy tárolórendszerképet, és lekérése és futtatta a rendszerképet a beállításjegyzékből. Folytassa az Azure Container Registry oktatóanyagokkal az ACR mélyebb megtekintéséhez.

> [!div class="nextstepaction"]
> [Azure Container Registry oktatóanyagok][container-registry-tutorial-quick-task]

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
