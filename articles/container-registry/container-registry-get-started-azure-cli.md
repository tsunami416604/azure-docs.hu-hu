---
title: Rövid útmutató – privát Docker-tárolójegyzék létrehozása az Azure-ban – Azure CLI-vel
description: Az útmutató azt ismerteti, hogyan hozhat létre egy privát Docker regisztrációs adatbázist az Azure CLI-vel.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: danlep
ms.custom: seodec18, H1Hack27Feb2017, mvc
ms.openlocfilehash: 37b1c8516268611a1174edfe20fef36dfb6b36c2
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55295831"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-cli"></a>Gyors útmutató: Az Azure CLI használatával privát tárolóregisztrációs adatbázis létrehozása

Az Azure Container Registry egy felügyelt Docker-tárolóregisztrációs adatbázis-szolgáltatás, amely a privát Docker-tárolók rendszerképeinek tárolására szolgál. Ez az útmutató az Azure Container Registry-példányok Azure CLI-vel való létrehozásának módját ismerteti. Ezután a Docker-parancsok segítségével továbbít egy rendszerképet a regisztrációs adatbázisba, és végül kérje le, és futtassa a rendszerképet a regisztrációs adatbázisból.

Ez a rövid útmutatóhoz, hogy futnak-e az Azure parancssori felület (2.0.55 verzió vagy újabb ajánlott). A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

A Dockert is telepítenie kell helyileg. A Docker csomagokat biztosít, amelyekkel a Docker egyszerűen konfigurálható bármely [macOS][docker-mac], [Windows][docker-windows] vagy [Linux][docker-linux] rendszeren.

Mivel az Azure Cloud Shell nem tartalmazza az összes szükséges Docker-összetevőt (a `dockerd`-démont), ehhez a rövid útmutatóhoz nem használható a Cloud Shell.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create][az-group-create] paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása

Ebben a rövid útmutatóban létrehozott egy *alapszintű* beállításjegyzékbe, amely költségek optimalizált megoldás megismerése az Azure Container Registry-fejlesztőknek. További információ az elérhető szolgáltatáscsomagban: [tároló-beállításjegyzék Termékváltozatai][container-registry-skus].

Hozzon létre egy ACR-példányt az [az acr create][az-acr-create] paranccsal. A beállításjegyzék nevének egyedinek kell lennie az Azure rendszerben, és 5–50 alfanumerikus karaktert kell tartalmaznia. Az alábbi példában a *myContainerRegistry007* nevet használjuk. Ezt cserélje le egy egyedi értékre.

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic
```

A tárolóregisztrációs adatbázis létrehozásakor a kimenet a következő példához hasonló:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2019-01-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "mycontainerregistry007.azurecr.io",
  "name": "myContainerRegistry007",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Jegyezze fel annak `loginServer` a kimenetben, azaz a teljes tartománynév (csak kisbetűvel). A rövid útmutató hátralevő részében az `<acrName>` elem helyettesíti a tárolóregisztrációs adatbázis nevét.

## <a name="log-in-to-registry"></a>Bejelentkezés a beállításjegyzékbe

Mozgatásához tárolórendszerképek, be kell jelentkezni a beállításjegyzékben. Ehhez használja az [az acr login][az-acr-login] parancsot.

```azurecli
az acr login --name <acrName>
```

A parancs a `Login Succeeded` üzenetet adja vissza, ha befejeződött.

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Tárolórendszerképek listázása

Az alábbi példa a regisztrációs adatbázisba adattárait listázza:

```azurecli
az acr repository list --name <acrName> --output table
```

Kimenet:

```
Result
----------------
busybox
```

Az alábbi példa címkéket listázza a **busybox** tárház.

```azurecli
az acr repository show-tags --name <acrName> --repository busybox --output table
```

Kimenet:

```
Result
--------
v1
```

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, használhatja a [az csoport törlése] [ az-group-delete] paranccsal eltávolítható az erőforráscsoport, a container registry és az ott tárolt tárolórendszerképek.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Azure Container Registry az Azure CLI-vel, leküldtünk bele egy tárolórendszerképet a regisztrációs adatbázisba, és kéri le, és futtatta a rendszerképet a beállításjegyzékből. Továbbra is az Azure Container Registry oktatóanyagok az ACR alaposabban.

> [!div class="nextstepaction"]
> [Azure Container Registry-oktatóanyagok][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
