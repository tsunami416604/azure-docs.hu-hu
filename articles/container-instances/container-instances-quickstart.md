---
title: Rövid útmutató – Az első Azure Container Instances-tároló létrehozása
description: Ebben a rövid útmutatóban az Azure CLI használatával helyez üzembe egy tárolót az Azure Container Instances szolgáltatásban
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: quickstart
ms.date: 03/19/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 57961cf1cb64f90cec7d2be90f3fbfe33344467d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-your-first-container-in-azure-container-instances"></a>Rövid útmutató: Az első tároló létrehozása az Azure Container Instances szolgáltatásban

Az Azure Container Instances segítségével egyszerűen hozhat létre és felügyelhet Docker-tárolókat az Azure-ban anélkül, hogy virtuális gépeket kellene kiépítenie, vagy egy magasabb szolgáltatási szintre kellene váltania. Ebben a rövid útmutatóban létrehozhat egy tárolót az Azure-ban, és közzéteheti az interneten egy teljes tartománynévvel (FQDN-nel). Ez a művelet egyetlen paranccsal hajtható végre. Néhány másodperc elteltével a következőt láthatja a böngészőben:

![Az Azure Container Instances használatával üzembe helyezett alkalmazás képe a böngészőben][aci-app-browser]

Ha nem rendelkezik Azure-előfizetéssel, első lépésként létrehozhat egy [ingyenes][azure-account] fiókot.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

A rövid útmutató teljesítéséhez használhatja az Azure Cloud Shellt vagy az Azure CLI helyileg telepített példányát. Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.27-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése][azure-cli-install].

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Azure Container Instancest, mint minden Azure-erőforrást, egy erőforráscsoportba kell helyezni. Ez egy olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az [az group create][az-group-create] paranccsal.

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Tároló létrehozása

A tároló létrehozásához meg kell adnia egy nevet, egy Docker-rendszerképet és egy Azure-erőforráscsoportot az [az container create][az-container-create] parancshoz. Ha szeretné, közzéteheti a tárolót az interneten egy DNS-név címke megadásával. Ebben az útmutatóban egy olyan tárolót fog üzembe helyezni, amely egy, a [Node.js][node-js] használatával létrehozott kisméretű webalkalmazást üzemeltet.

A tárolópéldány indításához futtassa az alábbi parancsot. A `--dns-name-label` értéknek egyedinek kell lennie abban az Azure-régióban, ahol a példányt létrehozza, így az egyediség biztosításához módosítania kell ezt az értéket.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/aci-helloworld --dns-name-label aci-demo --ports 80
```

Néhány másodperc elteltével válasz érkezik a kérésre. A tároló kezdetben **Létrehozás** állapotban van, de néhány másodpercen belül el kell indulnia. Az állapotot az [az container show][az-container-show] paranccsal ellenőrizheti:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
```

A parancs futtatásakor a tároló teljes tartományneve (FQDN) és annak kiépítési állapota jelenik meg:

```console
$ az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
FQDN                               ProvisioningState
---------------------------------  -------------------
aci-demo.eastus.azurecontainer.io  Succeeded
```

Miután a tároló átvált a **Sikeres** állapotba, elérhetővé válik a böngészőben a teljes tartománynév megkeresésével:

![Képernyőkép a böngészőről, ahol egy Azure-tárolópéldányban futó alkalmazás látható][aci-app-browser]

## <a name="pull-the-container-logs"></a>A tároló naplóinak lekérése

A létrehozott tároló naplóit az [az container logs][az-container-logs] paranccsal kérheti le:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

A következőhöz hasonló kimenetnek kell megjelennie:

```console
$ az container logs --resource-group myResourceGroup -n mycontainer
listening on port 80
::ffff:10.240.255.105 - - [15/Mar/2018:21:18:26 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
::ffff:10.240.255.105 - - [15/Mar/2018:21:18:26 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
```

## <a name="attach-output-streams"></a>Kimeneti stream csatolása

A naplók csatolása mellett, helyi standard kimeneti- és hibastreameket csatol a tárolóhoz.

Először csatolja a helyi konzol a tároló kimeneti steamjeihez az [az container attach][az-container-attach] paranccsal:

```azurecli-interactive
az container attach --resource-group myResourceGroup -n mycontainer
```

A csatolást követően frissítse a böngészőt néhány alkalommal, hogy további kimeneteket hozzon létre. Végül válassza le a konzolt a `Control+C` paranccsal. A következőhöz hasonló kimenetnek kell megjelennie:

```console
$ az container attach --resource-group myResourceGroup -n mycontainer
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2018-03-15 21:17:59+00:00) pulling image "microsoft/aci-helloworld"
(count: 1) (last timestamp: 2018-03-15 21:18:05+00:00) Successfully pulled image "microsoft/aci-helloworld"
(count: 1) (last timestamp: 2018-03-15 21:18:05+00:00) Created container with id 3534a1e2ee392d6f47b2c158ce8c1808d1686fc54f17de3a953d356cf5f26a45
(count: 1) (last timestamp: 2018-03-15 21:18:06+00:00) Started container with id 3534a1e2ee392d6f47b2c158ce8c1808d1686fc54f17de3a953d356cf5f26a45

Start streaming logs:
listening on port 80
::ffff:10.240.255.105 - - [15/Mar/2018:21:18:26 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
::ffff:10.240.255.105 - - [15/Mar/2018:21:18:26 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
::ffff:10.240.255.107 - - [15/Mar/2018:21:18:44 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
::ffff:10.240.255.107 - - [15/Mar/2018:21:18:47 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
```

## <a name="delete-the-container"></a>A tároló törlése

Miután végzett a tárolóval, az [az container delete][az-container-delete] parancs használatával távolíthatja el:

```azurecli-interactive
az container delete --resource-group myResourceGroup --name mycontainer
```

A tároló törlésének ellenőrzéséhez futtassa az [az container list](/cli/azure/container#az_container_list) parancsot:

```azurecli-interactive
az container list --resource-group myResourceGroup --output table
```

A **mycontainer** tárolónak nem szabad megjelennie a parancs kimenetében. Ha az erőforráscsoportban nincs másik tároló, akkor semmilyen kimenet nem jelenik meg.

## <a name="next-steps"></a>További lépések

A jelen rövid útmutatóban alkalmazott tároló kódja a Docker-fájljával egyetemben elérhető [a GitHubon][app-github-repo]. Ha próbaképpen szeretné maga létrehozni és üzembe helyezni az Azure Container Instances szolgáltatásban az Azure Container Registry használatával, folytassa az Azure Container Instances oktatóanyagával.

> [!div class="nextstepaction"]
> [Az Azure Container Instances oktatóanyagai](./container-instances-tutorial-prepare-app.md)

Ha ki szeretné próbálni a tárolók futtatásának különböző lehetőségeit egy Azure-beli előkészítési rendszerben, tekintse meg a [Service Fabric][service-fabric] vagy az [Azure Container Service (AKS)][container-service] rövid útmutatóit.

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - External -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[node-js]: http://nodejs.org

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az_container_attach
[az-container-create]: /cli/azure/container#az_container_create
[az-container-delete]: /cli/azure/container#az_container_delete
[az-container-list]: /cli/azure/container#az_container_list
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[az-group-create]: /cli/azure/group#az_group_create
[azure-cli-install]: /cli/azure/install-azure-cli
[container-service]: ../aks/kubernetes-walkthrough.md
[service-fabric]: ../service-fabric/service-fabric-quickstart-containers.md
