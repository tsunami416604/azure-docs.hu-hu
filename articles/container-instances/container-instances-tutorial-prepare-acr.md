---
title: Oktatóanyag – a tároló beállításjegyzékének előkészítése lemezkép üzembe helyezéséhez
description: Azure Container Instances oktatóanyag – 3. rész – Azure Container Registry előkészítése és rendszerkép küldése
ms.topic: tutorial
ms.date: 12/18/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: 1a5b9555572264b6a00b4ce73eaa0719d94fd99b
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "78252159"
---
# <a name="tutorial-create-an-azure-container-registry-and-push-a-container-image"></a>Oktatóanyag: Azure Container Registry létrehozása és tároló-rendszerkép leküldése

Ez az oktatóanyag egy háromrészes sorozat második része. Az oktatóanyag [első részében](container-instances-tutorial-prepare-app.md) egy Docker-tároló rendszerképét hoztuk létre egy Node.js-webalkalmazáshoz. Ebben az oktatóanyagban leküldéssel továbbítjuk a rendszerképet az Azure Container Registrybe. Ha még nem hozta létre a tárolórendszerképet, lépjen vissza az [1. oktatóanyag – Tárolórendszerképek létrehozása](container-instances-tutorial-prepare-app.md) részhez.

Az Azure Container Registry az Ön privát Docker-tárolójegyzéke az Azure-ban. Ebben az oktatóanyagban az adatsorozat második része:

> [!div class="checklist"]
> * Azure Container Registry-példány létrehozása az Azure CLI-vel
> * Egy tárolórendszerkép címkézése az Azure Container Registry-példányhoz
> * A rendszerkép feltöltése az adatbázisba

A következő cikkben, amely a sorozat utolsó oktatóanyaga, üzembe helyezzük a tárolót a privát tárolójegyzékből az Azure Container Instancesbe.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="create-azure-container-registry"></a>Azure Container Registry létrehozása

A tárolójegyzék létrehozásához szüksége lesz egy *erőforráscsoportra*, amelyben üzembe helyezheti azt. Az erőforráscsoport egy olyan logikai gyűjtemény, amelyben a rendszer üzembe helyezi és kezeli az összes Azure-erőforrást.

Hozzon létre egy erőforráscsoportot az [az group create][az-group-create] paranccsal. A következő példában egy *myResourceGroup* nevű erőforráscsoportot hozunk létre az *eastus* régióban:

```azurecli
az group create --name myResourceGroup --location eastus
```

Az erőforráscsoport létrehozása után hozzon létre egy Azure Container Registryt az [az acr create][az-acr-create] paranccsal. A tárolóregisztrációs adatbázis nevének egyedinek kell lennie az Azure rendszerében, és 5–50 alfanumerikus karakterből kell állnia. Cserélje le az `<acrName>` elemet az adatbázis egyedi nevére:

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Íme egy példa kimenet a *mycontainerregistry082* nevű új Azure Container Registryhez (itt csonkolva látható):

```output
...
{
  "creationDate": "2018-03-16T21:54:47.297875+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry082",
  "location": "eastus",
  "loginServer": "mycontainerregistry082.azurecr.io",
  "name": "mycontainerregistry082",
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

Az oktatóanyag hátralevő részében az `<acrName>` helyettesíti a tárolójegyzék ebben a lépésben választott nevét.

## <a name="log-in-to-container-registry"></a>Bejelentkezés a tárolójegyzékbe

Először be kell jelentkeznie az Azure Container Registrybe, mielőtt rendszerképeket küldhetne a tárolóregisztrációs adatbázisba. Használja az [az acr login][az-acr-login] parancsot a művelet befejezéséhez. Meg kell adnia a tárolójegyzék egyedi nevét, amelyet az adatbázis létrehozásakor választott.

```azurecli
az acr login --name <acrName>
```

Például:

```azurecli
az acr login --name mycontainerregistry082
```

A parancs a `Login Succeeded` üzenetet adja vissza, ha befejeződött:

```output
Login Succeeded
```

## <a name="tag-container-image"></a>Tárolórendszerkép címkézése

Ha a tárolójegyzék-rendszerképet egy privát tárolójegyzékbe kívánja leküldeni, mint az Azure Container Registry, akkor először fel kell címkéznie a rendszerképet a tárolójegyzék bejelentkezési kiszolgálójának teljes nevével.

Először is szerezze be az Azure Container Registry bejelentkezési kiszolgálójának teljes nevét. Futtassa a következő [az acr show][az-acr-show] parancsot, majd az `<acrName>` elemet cserélje az imént létrehozott tárolójegyzék nevére:

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Tehát ha a tárolójegyzék neve *mycontainerregistry082*:

```azurecli
az acr show --name mycontainerregistry082 --query loginServer --output table
```

```output
Result
------------------------
mycontainerregistry082.azurecr.io
```

Ezután jelenítse meg a helyi rendszerképek listáját a [docker images][docker-images] paranccsal:

```bash
docker images
```

A gépen található többi rendszerkép mellett szerepelnie kell az *aci-tutorial-app* rendszerképnek is, amelyet [az előző oktatóanyagban](container-instances-tutorial-prepare-app.md) hozott létre:

```console
$ docker images
REPOSITORY          TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app    latest    5c745774dfa9    39 minutes ago    68.1 MB
```

Címkézze fel az *ACI-tutorial-app* rendszerképet a tároló beállításjegyzékének bejelentkezési kiszolgálójával. Adja hozzá a `:v1` címkét a rendszerkép nevének végéhez, ami jelzi a rendszerkép verziószámát. Helyettesítse be az `<acrLoginServer>` helyére a korábban futtatott [az acr show][az-acr-show] parancs eredményét.

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

Futtassa ismét a `docker images` parancsot a címkézési művelet ellenőrzéséhez:

```console
$ docker images
REPOSITORY                                            TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app                                      latest    5c745774dfa9    39 minutes ago    68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app    v1        5c745774dfa9    7 minutes ago     68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>Rendszerkép leküldése az Azure Container Registrybe

Most, hogy megcímkézte az *ACI-tutorial-app* rendszerképet a privát beállításjegyzék teljes bejelentkezési kiszolgálójának nevével, leküldheti a rendszerképet a beállításjegyzékbe a [Docker push][docker-push] paranccsal. Helyettesítse be az `<acrLoginServer>` helyére a bejelentkezési kiszolgáló teljes nevét, amelyet az előző lépésben kért le.

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

A `push` művelet az internetkapcsolat sebességétől függően néhány másodperctől kezdve néhány percig is eltarthat, a kimenete pedig a következőhöz hasonló lesz:

```console
$ docker push mycontainerregistry082.azurecr.io/aci-tutorial-app:v1
The push refers to a repository [mycontainerregistry082.azurecr.io/aci-tutorial-app]
3db9cac20d49: Pushed
13f653351004: Pushed
4cd158165f4d: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:ed67fff971da47175856505585dcd92d1270c3b37543e8afd46014d328f05715 size: 1576
```

## <a name="list-images-in-azure-container-registry"></a>Az Azure Container Registryben található rendszerképek felsorolása

Annak ellenőrzésére, hogy az imént leküldött rendszerkép valóban az Ön Azure Container Registryjében található-e, jelenítse meg a tárolójegyzékben található rendszerképek listáját az [az acr repository list][az-acr-repository-list] paranccsal. Cserélje le az `<acrName>` kifejezést a tárolóregisztrációs adatbázis nevére.

```azurecli
az acr repository list --name <acrName> --output table
```

Például:

```azurecli
az acr repository list --name mycontainerregistry082 --output table
```

```output
Result
----------------
aci-tutorial-app
```

Egy adott rendszerkép *címkéinek* megtekintéséhez használja az [az acr repository show-tags][az-acr-repository-show-tags] parancsot.

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

A következőhöz hasonló kimenetnek kell megjelennie:

```console
az acr repository show-tags --name mycontainerregistry082 --repository aci-tutorial-app --output table
Result
--------
v1
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban előkészítettünk egy Azure Container Registryt az Azure Container Instancesben való használatra, és leküldtünk bele egy tárolórendszerképet. A következő lépéseket hajtotta végre:

> [!div class="checklist"]
> * Azure Container Registry példány létrehozása az Azure CLI-vel
> * Címkézett egy tárolórendszerképet az Azure Container Registryhez
> * Feltöltött egy rendszerképet az Azure Container Registrybe

Folytassa a következő oktatóanyaggal, amely azt ismerteti, hogyan helyezhető üzembe a tároló az Azure-ban az Azure Container Instances használatával:

> [!div class="nextstepaction"]
> [Tárolók üzembe helyezése az Azure Container Instances szolgáltatásban](container-instances-tutorial-deploy-app.md)

<!-- LINKS - External -->
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: https://nodejs.org

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository-list]: /cli/azure/acr/repository
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-group-create]: /cli/azure/group#az-group-create
[azure-cli-install]: /cli/azure/install-azure-cli
