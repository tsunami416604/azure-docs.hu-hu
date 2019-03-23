---
title: Rövid útmutató – Docker-tároló üzembe helyezése az Azure Container Instances - CLI
description: Ebben a rövid, az Azure CLI használatával helyezhet üzembe gyorsan tárolóalapú webalkalmazás, amely egy elkülönített Azure-tárolópéldányon
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 8e504a081f8685107871aed920077dd75a70dfa7
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368510"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-cli"></a>Gyors útmutató: Üzembe helyezéséhez az Azure-ban az Azure CLI használatával

Azure Container Instances használatával kiszolgáló nélküli Docker-tárolókat futtathat az Azure-ban egyszerű és gyors. Egy tároló példány igény szerinti-alkalmazás üzembe helyezése, ha már nincs szükség a teljes tárolót vezénylési platformot hasonlóan az Azure Kubernetes Service-ben.

Ebben a rövid útmutatóban az Azure CLI-elkülönített Docker-tároló üzembe helyezése, és a egy teljesen minősített tartománynevét (FQDN) elérhetővé az alkalmazás használja. Néhány másodpercet, egy egyetlen központi telepítési parancs végrehajtása után megnyithatja az a tárolóban futó alkalmazásnak:

![Az Azure Container Instances szolgáltatásban üzembe helyezett alkalmazás képe a böngészőben][aci-app-browser]

Ha nem rendelkezik Azure-előfizetéssel, első lépésként létrehozhat egy [ingyenes][azure-account] fiókot.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

A rövid útmutató teljesítéséhez használhatja az Azure Cloud Shellt vagy az Azure CLI helyileg telepített példányát. Ha a következőhöz szeretne használni, helyileg, 2.0.55 verzióját, vagy később ajánlott. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Azure Container Instancest – mint minden Azure-erőforrást – egy erőforráscsoportban kell üzembe helyezni. Az erőforráscsoportok lehetővé teszik az egymáshoz kapcsolódó Azure-erőforrások rendszerezését és kezelését.

Először hozzon létre egy erőforráscsoportot *myResourceGroup* néven az *eastus* helyen az alábbi [az group create][az-group-create] paranccsal:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Tároló létrehozása

Most, hogy már van egy erőforráscsoportja, futtathat egy tárolót az Azure-ban. Egy tárolópéldány Azure CLI-vel való létrehozásához adjon meg egy erőforráscsoport-nevet, egy tárolópéldánynevet és egy Docker-tárolórendszerképet az [az container create][az-container-create] parancsban. Ebben a rövid útmutatóban használhatja a nyilvános `mcr.microsoft.com/azuredocs/aci-helloworld` kép. Ez a rendszerkép csomagok a node.js-ben, amely egy statikus HTML-oldalt szolgál egy kisméretű webalkalmazást.

A tárolóit közzéteheti az interneten. Ehhez adjon meg egy vagy több megnyitni kívánt portot, egy DNS-névcímkét vagy mindkettőt. Ebben a rövid útmutatóban üzembe helyezi egy-egy DNS-névcímke tárolóban úgy, hogy a webalkalmazás nyilvánosan elérhető.

A tárolópéldány indításához az alábbihoz hasonló parancs végrehajtása. Állítsa be a `--dns-name-label` érték, amely egyedi az Azure-régióban, ahol létrehozhatja a példányt. Ha „A DNS-névcímke nem érhető el” hibaüzenetet kap, próbálkozzon másik DNS-névcímkével.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld --dns-name-label aci-demo --ports 80
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
::ffff:10.240.255.55 - - [21/Mar/2019:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```

## <a name="attach-output-streams"></a>Kimeneti stream csatolása

A naplók megtekintése mellett helyi standard kimeneti és hibastreameket csatolhat a tárolóhoz.

Először futtassa a következőt a [az container attach] [ az-container-attach] parancsot a helyi konzol csatlakoztatása a tároló kimeneti steamjeihez:

```azurecli-interactive
az container attach --resource-group myResourceGroup --name mycontainer
```

A csatolást követően frissítse a böngészőt néhány alkalommal, hogy további kimeneteket hozzon létre. Amikor kész, válassza le a konzolt a `Control+C` paranccsal. A következőhöz hasonló kimenetnek kell megjelennie:

```console
$ az container attach --resource-group myResourceGroup --name mycontainer
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 17:27:20+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-helloworld"
(count: 1) (last timestamp: 2019-03-21 17:27:24+00:00) Successfully pulled image "mcr.microsoft.com/azuredocs/aci-helloworld"
(count: 1) (last timestamp: 2019-03-21 17:27:27+00:00) Created container
(count: 1) (last timestamp: 2019-03-21 17:27:27+00:00) Started container

Start streaming logs:
listening on port 80

::ffff:10.240.255.55 - - [21/Mar/2019:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:47:01 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.56 - - [21/Mar/2019:17:47:12 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
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

Ebben a rövid útmutatóban létrehozott egy Azure Container Instances szolgáltatáshoz a Microsoft nyilvános rendszerkép használatával. Ha saját maga szeretne létrehozni és üzembe helyezni egy tárolórendszerképet egy Azure-beli privát tárolóregisztrációs adatbázisból, lépjen tovább az Azure Container Instances oktatóanyagára.

> [!div class="nextstepaction"]
> [Az Azure Container Instances oktatóanyaga](./container-instances-tutorial-prepare-app.md)

Próbálja ki az Azure-ban futó tárolók egy tárolószervező rendszer lehetőségei, tekintse meg a [Azure Kubernetes Service (AKS)] [ container-service] rövid útmutatók.

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - External -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[azure-account]: https://azure.microsoft.com/free/
[node-js]: https://nodejs.org

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
