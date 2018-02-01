---
title: "Az Azure Container Instances oktatóanyaga – Az Azure Container Registry előkészítése"
description: "Az Azure Container Instances oktatóanyaga, 2/3. rész – Az Azure Container Registry előkészítése"
services: container-instances
author: neilpeterson
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 94ecba44b8281460da4518c146aab814d2eaa850
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2018
---
# <a name="deploy-and-use-azure-container-registry"></a>Az Azure Container Registry üzembe helyezése és használata

Ez az oktatóanyag egy háromrészes sorozat második része. Az [előző lépésben](container-instances-tutorial-prepare-app.md) létrehoztunk egy tárolórendszerképet egy [Node.js][nodejs]-ben írt egyszerű webalkalmazáshoz. Ebben az oktatóanyagban leküldéssel továbbítjuk a rendszerképet egy Azure Container Registry tárolóregisztrációs adatbázisba. Ha még nem hozta létre a tárolórendszerképet, lépjen vissza az [1. oktatóanyag – Tárolórendszerkép létrehozása](container-instances-tutorial-prepare-app.md) részhez.

Az Azure Container Registry egy Azure-alapú privát regisztrációs adatbázis Docker-tárolórendszerképekhez. Ez az oktatóanyag azt ismerteti, hogyan lehet üzembe helyezni egy Azure Container Registry-példányt, és hogyan lehet továbbítani rá egy tárolórendszerképet.

A cikk, amely a sorozat második része, a következő lépésekből áll:

> [!div class="checklist"]
> * Egy Azure Container Registry-példány üzembe helyezése
> * Egy tárolórendszerkép címkézése az Azure Container Registry-példányhoz
> * A rendszerkép feltöltése az adatbázisba

A következő cikkben, amely a sorozat utolsó oktatóanyaga, telepítjük a tárolót a privát regisztrációs adatbázisból az Azure Container Instances rendszerébe.

## <a name="before-you-begin"></a>Előkészületek

Az oktatóanyaghoz az Azure CLI 2.0.23-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése][azure-cli-install].

Az oktatóanyag elvégzéséhez szüksége lesz egy helyileg telepített Docker-fejlesztési környezetre. A Docker csomagokat biztosít, amelyekkel a Docker egyszerűen konfigurálható bármely [Mac][docker-mac], [Windows][docker-windows] vagy [Linux][docker-linux] rendszeren.

Az Azure Cloud Shell nem tartalmazza a jelen oktatóanyag lépéseinek elvégzéséhez szükséges Docker-összetevőket, ezért az oktatóanyag teljesítéséhez telepítenie kell a számítógépén az Azure CLI-t és a Docker-fejlesztési környezetet.

## <a name="deploy-azure-container-registry"></a>Azure Container Registry üzembe helyezése

Az Azure Container Registry üzembe helyezéséhez először is szükség van egy erőforráscsoportra. Az Azure-erőforráscsoport egy olyan logikai gyűjtemény, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az [az group create][az-group-create] paranccsal. Ebben a példában egy *myResourceGroup* nevű erőforráscsoportot hozunk létre az *eastus* régióban.

```azurecli
az group create --name myResourceGroup --location eastus
```

Hozzon létre egy Azure tárolóregisztrációs adatbázist az [az acr create][az-acr-create] paranccsal. A tárolóregisztrációs adatbázis nevének egyedinek kell lennie az Azure rendszerében, és 5–50 alfanumerikus karakterből kell állnia. Cserélje le az `<acrName>` elemet az adatbázis egyedi nevére:

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Példa egy *mycontainerregistry082* nevű Azure tárolóregisztrációs adatbázis létrehozására:

```azurecli
az acr create --resource-group myResourceGroup --name mycontainerregistry082 --sku Basic --admin-enabled true
```

Az oktatóanyag hátralevő részében az `<acrName>` elem helyettesíti a tárolóregisztrációs adatbázis választott nevét.

## <a name="container-registry-login"></a>Bejelentkezés a tárolóregisztrációs adatbázisba

Először be kell jelentkeznie az Azure Container Registrybe, mielőtt rendszerképeket küldhetne a tárolóregisztrációs adatbázisba. Ehhez a művelethez használja az [az acr login][az-acr-login] parancsot. Meg kell adnia a tárolóregisztrációs adatbázis egyedi nevét, amelyet az adatbázis létrehozásakor adott meg.

```azurecli
az acr login --name <acrName>
```

A parancs a `Login Succeeded` üzenetet adja vissza, ha befejeződött.

## <a name="tag-container-image"></a>Tárolórendszerkép címkézése

Ha egy privát regisztrációs adatbázisban található tárolórendszerképet kíván telepíteni, akkor fel kell címkéznie a rendszerképet a regisztrációs adatbázis `loginServer` nevével.

A meglévő rendszerképek listájának megtekintéséhez használja a [docker images][docker-images] parancsot.

```bash
docker images
```

Kimenet:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

A bejelentkezési kiszolgáló nevének lekéréséhez futtassa az [az acr show][az-acr-show] parancsot. Cserélje le az `<acrName>` kifejezést a tárolóregisztrációs adatbázis nevére.

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Példa a kimenetre:

```
Result
------------------------
mycontainerregistry082.azurecr.io
```

Címkézze fel az *aci-tutorial-app* rendszerképet a tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának nevével. Adja hozzá a `:v1` kifejezést a rendszerkép nevének végéhez. Ez a címke a rendszerkép verziószámát jelöli. Helyettesítse be az `<acrLoginServer>` helyére az imént futtatott [az acr show][az-acr-show] parancs eredményét.

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

Ha elkészült a címkézéssel, futtassa a `docker images` parancsot a művelet ellenőrzéséhez.

```bash
docker images
```

Kimenet:

```bash
REPOSITORY                                                TAG                 IMAGE ID            CREATED             SIZE
aci-tutorial-app                                          latest              5c745774dfa9        39 seconds ago      68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app        v1                  a9dace4e1a17        7 minutes ago       68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>Rendszerkép leküldése az Azure Container Registrybe

Küldje el az *aci-tutorial-app* rendszerképet a regisztrációs adatbázisba a [docker push][docker-push] paranccsal. Helyettesítse be az `<acrLoginServer>` helyére a bejelentkezési kiszolgáló teljes nevét, amelyet az előző lépésben kért le.

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

A `push` művelet az internetkapcsolat sebességétől függően néhány másodperctől kezdve néhány percig is eltarthat, a kimenete pedig a következőhöz hasonló lesz:

```bash
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

Az Azure Container Registrybe leküldött rendszerképek listájának lekéréséhez használja az [az acr repository list][az-acr-repository-list] parancsot. Frissítse a parancsot a tárolóregisztrációs adatbázis nevével.

```azurecli
az acr repository list --name <acrName> --output table
```

Kimenet:

```azurecli
Result
----------------
aci-tutorial-app
```

Ezután egy adott rendszerkép címkéinek megtekintéséhez használja az [az acr repository show-tags][az-acr-repository-show-tags] parancsot.

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

Kimenet:

```azurecli
Result
--------
v1
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban előkészítettünk egy Azure Container Registry tárolóregisztrációs adatbázist az Azure Container Instances szolgáltatásban való használatra, és leküldtünk bele egy tárolórendszerképet. A következő lépéseket hajtotta végre:

> [!div class="checklist"]
> * Telepített egy Azure Container Registry-példányt
> * Címkézett egy tárolórendszerképet az Azure Container Registryhez
> * Feltöltött egy rendszerképet az Azure Container Registrybe

Folytassa a következő oktatóanyaggal, amely azt ismerteti, hogyan helyezhető üzembe a tároló az Azure-ban az Azure Container Instances használatával.

> [!div class="nextstepaction"]
> [Tárolók üzembe helyezése az Azure Container Instances szolgáltatásban](./container-instances-tutorial-deploy-app.md)

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
[nodejs]: http://nodejs.org

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-repository-list]: /cli/azure/acr/repository#az_acr_list
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az_acr_repository_show_tags
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-group-create]: /cli/azure/group#az_group_create
[azure-cli-install]: /cli/azure/install-azure-cli
