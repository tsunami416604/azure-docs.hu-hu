---
title: Az Azure-on futó Kubernetes oktatóanyaga – Tárolóregisztrációs adatbázis létrehozása
description: Az Azure Kubernetes Service (AKS) jelen oktatóanyagában egy Azure Container Registry-példányt hozhat létre, és feltöltheti egy mintaalkalmazás tárolórendszerképét.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 5089326af1d7f6e057667cd916f35de92bf517ef
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614246"
---
# <a name="tutorial-deploy-and-use-azure-container-registry"></a>Oktatóanyag: Az Azure Container Registry üzembe helyezése és használata

Az Azure Container Registry (ACR) egy privát beállításjegyzék a rendszerképek. A privát tárolóregisztrációs adatbázisok segítségével biztonságosan állíthatja össze és helyezheti üzembe alkalmazásait és egyéni kódját. Ez az oktatóanyag egy hétrészes sorozat második része, és azt ismerteti, hogyan lehet üzembe helyezni egy ACR-példányt, és hogyan lehet továbbítani rá egy tárolórendszerképet. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Azure Container Registry (ACR)-példány létrehozása
> * Egy tárolórendszerkép címkézése az ACR-hez
> * A rendszerkép feltöltése az ACR-be
> * A regisztrációs adatbázisban lévő rendszerképek megtekintése

A további oktatóanyagokban az ACR-példányba integrálva van az aks-ben a Kubernetes-fürt, és a egy alkalmazás központi telepítése a lemezképből.

## <a name="before-you-begin"></a>Előkészületek

Az [előző lépésben][aks-tutorial-prepare-app] létrehoztunk egy tárolórendszerképet egy egyszerű Azure-szavazóalkalmazáshoz. Ha még nem hozta létre az Azure-szavazóalkalmazás rendszerképét, lépjen vissza az [1. oktatóanyag – Tárolórendszerképek létrehozása][aks-tutorial-prepare-app] részhez.

Ehhez az oktatóanyaghoz, hogy futtat-e az Azure CLI 2.0.53 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="create-an-azure-container-registry"></a>Azure Container Registry létrehozása

Az Azure Container Registry létrehozásához először is egy erőforráscsoportra lesz szükség. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az [az group create][az-group-create] paranccsal. A következő példában egy *myResourceGroup* nevű erőforráscsoportot hozunk létre az *eastus* régióban:

```azurecli
az group create --name myResourceGroup --location eastus
```

Hozzon létre egy Azure Container Registry-példány az a [az acr létrehozása][az-acr-create] parancsot, és adja meg a saját beállításjegyzék nevét. A beállításjegyzék nevének egyedinek kell lennie az Azure rendszerben, és 5–50 alfanumerikus karaktert kell tartalmaznia. Az oktatóanyag hátralevő részében az `<acrName>` elem helyettesíti a tárolóregisztrációs adatbázis nevét. Adja meg a saját egyedi nevét. Az *Alapszintű* termékváltozat költséghatékony, fejlesztési célú belépési pontként szolgál, és kiegyenlített tárolási kapacitást és teljesítményt biztosít.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

## <a name="log-in-to-the-container-registry"></a>Bejelentkezés a tárolóregisztrációs adatbázisba

Az ACR-példány használatához először be kell jelentkeznie. Használja a [az acr bejelentkezési][az-acr-login] parancsot, és adja meg a tárolóregisztrációs adatbázisba, az előző lépésben megadott egyedi nevét.

```azurecli
az acr login --name <acrName>
```

A parancs a *Bejelentkezés sikeres* üzenetet adja vissza, ha befejeződött.

## <a name="tag-a-container-image"></a>A tárolórendszerképek címkézése

Az aktuális helyi rendszerképek listájának megtekintéséhez használja a [docker-rendszerképek][docker-images] parancsot:

```
$ docker images

REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front             latest              4675398c9172        13 minutes ago      694MB
redis                        latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        9 months ago        694MB
```

Az *azure-vote-front* tárolórendszerkép ACR-ben való használatához a rendszerképet fel kell címkézni a tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának címével. Ezt a címkét a rendszer az útválasztáshoz használja, amikor tárolórendszerképeket küld le egy regisztrációs adatbázisba.

A bejelentkezési kiszolgáló címének lekéréséhez használja a [az acr list][az-acr-list] parancsot, és lekérdezi a *adatbázis bejelentkezési kiszolgálójának nevével* módon:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Ezután címkézze fel a helyi *azure-vote-front* rendszerképet a tárolóregisztrációs adatbázis *acrloginServer* címével. A rendszerkép verziójának jelölésére adja hozzá a *:v1* címkét a rendszerkép nevének végéhez:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

A címkék alkalmazása ellenőrzéséhez futtassa [docker-rendszerképek][docker-images] újra. Ez felcímkézi a rendszerképet az ACR-példány címével és egy verziószámmal.

```
$ docker images

REPOSITORY                                           TAG           IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest        eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry.azurecr.io/azure-vote-front      v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest        a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask         788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-registry"></a>Rendszerképek leküldése a regisztrációs adatbázisba

A rendszerkép összeállítása és címkézése és leküldése a *azure-vote-front* rendszerképet az ACR-példányba. Használat [docker leküldéses][docker-push] , és adja meg a saját *acrLoginServer* oldja meg a rendszerkép nevének a következő:

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

A rendszerkép ACR-be való leküldése eltarthat néhány percig.

## <a name="list-images-in-registry"></a>A regisztrációs adatbázisban található rendszerképek felsorolása

Az ACR-példányba leküldött rendszerképek listájának lekéréséhez használja a [az acr-tárház lista][az-acr-repository-list] parancsot. Adja meg a saját `<acrName>` adatbázisnevét:

```azurecli
az acr repository list --name <acrName> --output table
```

A következő példakimeneten az *azure-vote-front* rendszerkép szerepel a regisztrációs adatbázis listájában:

```
Result
----------------
azure-vote-front
```

Egy adott rendszerkép címkéinek megtekintéséhez használja a [az acr tárház show-tags][az-acr-repository-show-tags] paranccsal a következőképpen:

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

A következő példakimeneten az egyik előző lépésben felcímkézett *v1* rendszerkép látható:

```
Result
--------
v1
```

Így most rendelkezik egy privát Azure Container Registry-példányon tárolt tárolórendszerképpel. Ezt a rendszerképet telepítjük az ACR-ből egy Kubernetes-fürtre a következő oktatóanyagban.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure Container Registry tárolóregisztrációs adatbázist hozott létre, és leküldött egy rendszerképet egy AKS-fürtben való használatra. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Azure Container Registry (ACR)-példány létrehozása
> * Egy tárolórendszerkép címkézése az ACR-hez
> * A rendszerkép feltöltése az ACR-be
> * A regisztrációs adatbázisban lévő rendszerképek megtekintése

Folytassa a következő oktatóanyaggal, amely azt ismerteti, hogyan helyezhető üzembe egy Kubernetes-fürt az Azure-ban.

> [!div class="nextstepaction"]
> [Kubernetes-fürt üzembe helyezése][aks-tutorial-deploy-cluster]

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr
[az-acr-list]: /cli/azure/acr
[az-acr-login]: https://docs.microsoft.com/cli/azure/acr#az-acr-login
[az-acr-list]: https://docs.microsoft.com/cli/azure/acr#az-acr-list
[az-acr-repository-list]: /cli/azure/acr/repository
[az-acr-repository-show-tags]: /cli/azure/acr/repository
[az-group-create]: /cli/azure/group#az-group-create
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-tutorial-deploy-cluster]: ./tutorial-kubernetes-deploy-cluster.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
