---
title: Rövid útmutató – Alkalmazás futtatása az Azure Container Instances szolgáltatásban
description: Ebben a rövid útmutatóban az Azure CLI használatával helyez üzembe egy Docker-tárolóban futó alkalmazást az Azure Container Instances szolgáltatásban
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: quickstart
ms.date: 10/02/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 7db3d9a076fe9ff5b8bbf970705b82a3f0d5ce54
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48855663"
---
# <a name="quickstart-run-an-application-in-azure-container-instances"></a>Rövid útmutató: Alkalmazás futtatása az Azure Container Instances szolgáltatásban

Az Azure Container Instances segítségével egyszerűen és gyorsan futtathat Docker-tárolókat az Azure-ban. Nem kell virtuális gépeket üzembe helyeznie vagy teljes körű tárolóvezérlési platformot használnia (amilyen például a Kubernetes). Ebben a rövid útmutatóban létrehoz egy tárolót az Azure-ban az Azure Portal használatával, majd elérhetővé teszi a hozzá tartozó alkalmazást egy teljes tartománynévvel (FQDN). Néhány másodperccel azután, hogy végrehajt egy üzembehelyezési parancsot, már meg is keresheti tallózással a futó alkalmazást:

![Az Azure Container Instances szolgáltatásban üzembe helyezett alkalmazás képe a böngészőben][aci-app-browser]

Ha nem rendelkezik Azure-előfizetéssel, első lépésként létrehozhat egy [ingyenes][azure-account] fiókot.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

A rövid útmutató teljesítéséhez használhatja az Azure Cloud Shellt vagy az Azure CLI helyileg telepített példányát. Ha helyben szeretné használni, a 2.0.27-es vagy újabb verzióra lesz szüksége. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Azure Container Instancest – mint minden Azure-erőforrást – egy erőforráscsoportban kell üzembe helyezni. Az erőforráscsoportok lehetővé teszik az egymáshoz kapcsolódó Azure-erőforrások rendszerezését és kezelését.

Először hozzon létre egy erőforráscsoportot *myResourceGroup* néven az *eastus* helyen az alábbi [az group create][az-group-create] paranccsal:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Tároló létrehozása

Most, hogy már van egy erőforráscsoportja, futtathat egy tárolót az Azure-ban. Egy tárolópéldány Azure CLI-vel való létrehozásához adjon meg egy erőforráscsoport-nevet, egy tárolópéldánynevet és egy Docker-tárolórendszerképet az [az container create][az-container-create] parancsban. A tárolóit közzéteheti az interneten. Ehhez adjon meg egy vagy több megnyitni kívánt portot, egy DNS-névcímkét vagy mindkettőt. Ebben az útmutatóban egy tárolót fog üzembe helyezni egy olyan DNS-névcímkével, amely egy, a Node.js használatával létrehozott kisméretű webalkalmazást üzemeltet.

A tárolópéldány indításához futtassa az alábbi parancsot. A `--dns-name-label` értéknek egyedinek kell lennie abban az Azure-régióban, ahol a példányt létrehozza. Ha „A DNS-névcímke nem érhető el” hibaüzenetet kap, próbálkozzon másik DNS-névcímkével.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/aci-helloworld --dns-name-label aci-demo --ports 80
```

Pár másodpercen belül az üzembe helyezés befejezéséről tájékoztató választ kell kapnia az Azure CLI-ről. Az állapotát az [az container show][az-container-show] paranccsal ellenőrizheti:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
```

A parancs futtatásakor a tároló teljes tartományneve (FQDN) és annak kiépítési állapota jelenik meg.

```console
$ az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
FQDN                               ProvisioningState
---------------------------------  -------------------
aci-demo.eastus.azurecontainer.io  Succeeded
```

Ha a tároló `ProvisioningState` paramétere **Succeeded** (Sikeres) értékű, navigáljon a teljes tartománynevéhez a böngészőben. Ha egy, az alábbihoz hasonló weboldal jelenik meg, gratulálunk! Sikeresen üzembe helyezett egy Docker-tárolóban futó alkalmazást az Azure-ban.

![Képernyőkép a böngészőről, ahol egy Azure-tárolópéldányban futó alkalmazás látható][aci-app-browser]

Ha az alkalmazás nem indul el azonnal, lehet, hogy csak várnia kell pár másodpercet, amíg a DNS elvégzi a propagálást. Ezt követően próbálja ismét frissíteni a böngészőjét.

## <a name="pull-the-container-logs"></a>A tároló naplóinak lekérése

Amikor hibaelhárítást kell végrehajtania egy tárolón, vagy az abban futtatott alkalmazáson (vagy csak meg kell néznie a kimenetet), először tekintse meg a tárolópéldány naplóit.

A tárolópéldány naplóit az [az container logs][az-container-logs] paranccsal hívhatja le:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

A kimenet megjeleníti a tároló naplóit, és a HTTP GET kéréseknek is meg kell jelenniük, amelyek akkor jöttek létre, amikor az alkalmazást a böngészőjében megtekintette.

```console
$ az container logs --resource-group myResourceGroup --name mycontainer
listening on port 80
::ffff:10.240.255.105 - - [01/Oct/2018:18:25:51 +0000] "GET / HTTP/1.0" 200 1663 "-" "-"
::ffff:10.240.255.106 - - [01/Oct/2018:18:31:04 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36"
::ffff:10.240.255.106 - - [01/Oct/2018:18:31:04 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36"
```

## <a name="attach-output-streams"></a>Kimeneti stream csatolása

A naplók megtekintése mellett helyi standard kimeneti és hibastreameket csatolhat a tárolóhoz.

Először csatolja a helyi konzol a tároló kimeneti steamjeihez az [az container attach][az-container-attach] paranccsal:

```azurecli-interactive
az container attach --resource-group myResourceGroup -n mycontainer
```

A csatolást követően frissítse a böngészőt néhány alkalommal, hogy további kimeneteket hozzon létre. Amikor kész, válassza le a konzolt a `Control+C` paranccsal. A következőhöz hasonló kimenetnek kell megjelennie:

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

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután végzett a tárolóval, az [az container delete][az-container-delete] parancs használatával távolíthatja el:

```azurecli-interactive
az container delete --resource-group myResourceGroup --name mycontainer
```

A tároló törlésének ellenőrzéséhez futtassa az [az container list](/cli/azure/container#az-container-list) parancsot:

```azurecli-interactive
az container list --resource-group myResourceGroup --output table
```

A **mycontainer** tárolónak nem szabad megjelennie a parancs kimenetében. Ha az erőforráscsoportban nincs másik tároló, akkor semmilyen kimenet nem jelenik meg.

Ha végzett a *myResourceGroup* erőforráscsoporttal és az abban lévő erőforrásokkal, törölje le az [az group delete][az-group-delete] paranccsal:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy tárolópéldányt egy, a nyilvános Docker Hub regisztrációs adatbázisban található rendszerkép használatával. Ha saját maga szeretne létrehozni és üzembe helyezni egy tárolórendszerképet egy Azure-beli privát tárolóregisztrációs adatbázisból, lépjen tovább az Azure Container Instances oktatóanyagára.

> [!div class="nextstepaction"]
> [Az Azure Container Instances oktatóanyaga](./container-instances-tutorial-prepare-app.md)

Ha ki szeretné próbálni a tárolók futtatásának különböző lehetőségeit egy Azure-beli előkészítési rendszerben, tekintse meg a [Service Fabric][service-fabric] vagy az [Azure Kubernetes Service (AKS)][container-service] rövid útmutatóit.

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - External -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[azure-account]: https://azure.microsoft.com/free/
[node-js]: http://nodejs.org

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-container-list]: /cli/azure/container#az-container-list
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[container-service]: ../aks/kubernetes-walkthrough.md
[service-fabric]: ../service-fabric/service-fabric-quickstart-containers.md
