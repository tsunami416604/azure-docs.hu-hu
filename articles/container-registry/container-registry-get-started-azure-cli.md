---
title: "Gyors üzembe helyezés – hozzon létre egy saját Docker beállításjegyzék az Azure-ban az Azure parancssori felület"
description: "Gyorsan bemutatják, hogyan hozzon létre egy saját Docker-tároló beállításjegyzék az Azure parancssori felület."
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: quicksart
ms.date: 12/07/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: f31f4e5e2b3fe5db85873894a7f2fa9c415392c1
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="create-a-container-registry-using-the-azure-cli"></a>Tároló-beállításjegyzék létrehozása az Azure CLI-vel

Az Azure Container Registry egy felügyelt Docker-tárolóregisztrációs adatbázis-szolgáltatás, amely a privát Docker-tárolók rendszerképeinek tárolására szolgál. Ez az útmutató adatokat egy Azure-tároló beállításjegyzék példányának létrehozásakor az Azure parancssori felület használatával.

A gyors üzembe helyezés megköveteli, hogy futnak-e az Azure parancssori felület 2.0.21 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha szeretné telepíteni vagy frissíteni, lásd: [Azure CLI 2.0 telepítése][azure-cli].

Helyileg telepített Docker is rendelkeznie kell. Docker biztosít, amely egyszerű konfigurálását a Docker bármely csomagok [Mac][docker-mac], [Windows][docker-windows], vagy [Linux] [ docker-linux] rendszer.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create][az-group-create] paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása

A gyors üzembe helyezés, hogy hozzon létre egy *alapvető* beállításjegyzék. Azure tároló beállításjegyzék több különböző Termékváltozatai, a következő táblázat ismerteti a röviden érhető el. Minden egyes kiterjesztett részletekért lásd: [tároló beállításjegyzék termékváltozatok][container-registry-skus].

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

Hozzon létre egy ACR példány használatával a [az acr létrehozása] [ az-acr-create] parancsot.

A beállításjegyzék neve **egyedinek kell lennie**. Az alábbi példában *myContainerRegistry007* szolgál. Ez egy egyedi értékre módosítani.

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

A gyors üzembe helyezés a többi, egész használjuk `<acrName>` számára a tároló neve.

## <a name="log-in-to-acr"></a>Jelentkezzen be ACR

A tárolórendszerképek mozgatásához először be kell jelentkeznie az ACR-példányba. Ehhez használja a [az acr bejelentkezési] [ az-acr-login] parancsot.

```azurecli
az acr login --name <acrName>
```

A parancs visszaadja a `Login Succeeded` üzenet amint befejeződött.

## <a name="push-image-to-acr"></a>ACR leküldéses lemezképet

Kép leküldése egy Azure-tárolóba beállításjegyzék, először egy lemezképet kell rendelkeznie. Ha még nem rendelkezik helyi tároló képeket, a következő parancsot egy meglévő lemezképet lekérés a Docker központ.

```bash
docker pull microsoft/aci-helloworld
```

Mielőtt tolható ki a lemezkép a beállításjegyzéket, a ACR bejelentkezési kiszolgáló teljesen minősített nevű kell címkét. A következő parancsot a teljes bejelentkezési kiszolgálónév ACR példány beszerzéséhez.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

A lemezkép használatával címkét a [docker címke] [ docker-tag] parancsot. Cserélje le `<acrLoginServer>` ACR példány bejelentkezési kiszolgáló nevével.

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

Végül [docker leküldéses] [ docker-push] a kép leküldéses ACR példányához. Cserélje le `<acrLoginServer>` ACR példány bejelentkezési kiszolgáló nevével.

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="list-container-images"></a>Tárolórendszerképek listázása

Az alábbi példa felsorolja a tárolóhelyekkel a beállításjegyzékben:

```azurecli
az acr repository list --name <acrName> --output table
```

Kimenet:

```bash
Result
----------------
aci-helloworld
```

Az alábbi példa felsorolja a címkék a a **aci-helloworld** tárházba.

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

Ha már nincs szüksége, használhatja a [az csoport törlése] [ az-group-delete] parancs beírásával távolítsa el az erőforráscsoportot, ACR példány és az összes tároló-lemezképeket.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

A gyors üzembe helyezés, az az Azure-tároló beállításjegyzék az Azure parancssori felület segítségével létrehozott. Ha azt szeretné, Azure tároló beállításjegyzék használatát Azure tároló osztályt, továbbra is az Azure-tároló példányok oktatóanyag.

> [!div class="nextstepaction"]
> [Azure tároló példányok útmutató][container-instances-tutorial-prepare-app]

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