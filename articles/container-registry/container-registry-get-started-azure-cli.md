---
title: "Rövid útmutató – Privát Docker regisztrációs adatbázis létrehozása az Azure-ban az Azure CLI-vel"
description: "Az útmutató azt ismerteti, hogyan hozhat létre egy privát Docker regisztrációs adatbázist az Azure CLI-vel."
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 12/07/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: a74a1ce5c9401d6445f5feec4af8d5cb771d2c64
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2018
---
# <a name="create-a-container-registry-using-the-azure-cli"></a>Tároló-beállításjegyzék létrehozása az Azure CLI-vel

Az Azure Container Registry egy felügyelt Docker-tárolóregisztrációs adatbázis-szolgáltatás, amely a privát Docker-tárolók rendszerképeinek tárolására szolgál. Ez az útmutató az Azure Container Registry-példányok Azure CLI-vel való létrehozásának módját ismerteti.

A rövid útmutatóhoz az Azure CLI 2.0.25-ös vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése][azure-cli].

A Dockert is telepítenie kell helyileg. A Docker csomagokat biztosít, amelyekkel a Docker egyszerűen konfigurálható bármely [Mac][docker-mac], [Windows][docker-windows] vagy [Linux][docker-linux] rendszeren.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create][az-group-create] paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása

Ebben a rövid útmutatóban egy *Alapszintű* beállításjegyzéket hozunk létre. Az Azure Container Registry több különböző termékváltozatban érhető el, amelyek rövid leírása az alábbi táblázatban található. Bővebb részletekért lásd a [Container Registry termékváltozatait][container-registry-skus] ismertető cikket.

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

Hozzon létre egy ACR-példányt az [az acr create][az-acr-create] paranccsal.

A beállításjegyzék nevének egyedinek kell lennie az Azure rendszerben, és 5–50 alfanumerikus karaktert kell tartalmaznia. Az alábbi példában a *myContainerRegistry007* nevet használjuk. Ezt cserélje le egy egyedi értékre.

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic
```

A tárolóregisztrációs adatbázis létrehozásakor a kimenet a következő példához hasonló:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-09-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "myContainerRegistry007.azurecr.io",
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

A rövid útmutató hátralevő részében az `<acrName>` elem helyettesíti a tárolóregisztrációs adatbázis nevét.

## <a name="log-in-to-acr"></a>Jelentkezzen be az ACR-be

A tárolórendszerképek mozgatásához először be kell jelentkeznie az ACR-példányba. Ehhez használja az [az acr login][az-acr-login] parancsot.

```azurecli
az acr login --name <acrName>
```

A parancs a `Login Succeeded` üzenetet adja vissza, ha befejeződött.

## <a name="push-image-to-acr"></a>Rendszerkép leküldése az ACR-be

Ahhoz, hogy rendszerképet tudjon küldeni egy Azure Container Registry tárolóregisztrációs adatbázisba, először szüksége van egy rendszerképre. Ha még nincs egy helyi rendszerképe sem, futtassa a következő parancsot egy meglévő rendszerkép lekéréshez a Docker Hubból.

```bash
docker pull microsoft/aci-helloworld
```

Mielőtt leküldhetné a rendszerképet a regisztrációs adatbázisba, fel kell címkéznie az ACR bejelentkezési kiszolgálójának teljes nevével. A következő paranccsal kérheti le az ACR-példány bejelentkezési kiszolgálójának teljes nevét.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Címkézze fel a rendszerképet a [docker tag][docker-tag] parancs használatával. Helyettesítse be az `<acrLoginServer>` helyére az ACR-példány bejelentkezési kiszolgálójának nevét.

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

Végül a [docker push][docker-push] paranccsal küldje le a rendszerképet az ACR-példányba. Helyettesítse be az `<acrLoginServer>` helyére az ACR-példány bejelentkezési kiszolgálójának nevét.

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="list-container-images"></a>Tárolórendszerképek listázása

A következő példa egy regisztrációs adatbázisba adattárait listázza:

```azurecli
az acr repository list --name <acrName> --output table
```

Kimenet:

```bash
Result
----------------
aci-helloworld
```

A következő példa az **aci-helloworld** adattárban lévő címkéket sorolja fel.

```azurecli
az acr repository show-tags --name <acrName> --repository aci-helloworld --output table
```

Kimenet:

```bash
Result
--------
v1
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, az [az group delete][az-group-delete] paranccsal eltávolítható az erőforráscsoport, az ACR-példány és az összes tárolórendszerkép.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Azure-beli tároló-beállításjegyzéket az Azure CLI segítségével. Ha az Azure Container Registry és az Azure Container Instances együttes használatának módját szeretné megismerni, folytassa az Azure Container Instances oktatóanyagával.

> [!div class="nextstepaction"]
> [Az Azure Container Instances oktatóanyaga][container-instances-tutorial-prepare-app]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli]: /cli/azure/install-azure-cli
[container-instances-tutorial-prepare-app]: ../container-instances/container-instances-tutorial-prepare-app.md
[container-registry-skus]: container-registry-skus.md