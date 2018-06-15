---
title: Az Azure Container Instances oktatóanyaga – Alkalmazás üzembe helyezése
description: Az Azure Container Instances oktatóanyaga, 3/3. rész – Alkalmazás üzembe helyezése
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: tutorial
ms.date: 03/21/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 1268ed0f5c46e0f65e1bf5381f2baca95fa0b915
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34610599"
---
# <a name="tutorial-deploy-a-container-to-azure-container-instances"></a>Oktatóanyag: Tároló üzembe helyezése az Azure Container Instancesben

Ez az egy háromrészes sorozat utolsó oktatóanyaga. Az előző oktatóanyagokban [létrehoztunk egy tárolórendszerképet](container-instances-tutorial-prepare-app.md), és [leküldtük az Azure Container Registrybe](container-instances-tutorial-prepare-acr.md). A sorozat befejező cikke a tároló üzembe helyezését ismerteti az Azure Container Instancesben.

Ebben az oktatóanyagban az alábbiakat végezte el:

> [!div class="checklist"]
> * A tároló üzembe helyezése az Azure Container Registryből az Azure Container Instancesbe
> * A futó alkalmazás megtekintése a böngészőben
> * A tároló naplóinak megjelenítése

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="deploy-the-container-using-the-azure-cli"></a>Tároló üzembe helyezése az Azure CLI használatával

Ebben a szakaszban az Azure CLI használatával üzembe helyezi [az első oktatóanyagban](container-instances-tutorial-prepare-app.md) létrehozott rendszerképet, amelyet [a második oktatóanyagban](container-instances-tutorial-prepare-acr.md) leküldött az Azure Container Registrybe. Mielőtt folytatná, győződjön meg arról, hogy mindkét oktatóanyagot elvégezte.

### <a name="get-registry-credentials"></a>A tárolójegyzék hitelesítő adatainak lekérése

Amennyiben egy privát tárolójegyzékben (mint amelyet [a második oktatóanyagban létrehozott](container-instances-tutorial-prepare-acr.md)) üzemeltetett rendszerképet kíván üzembe helyezni, meg kell adnia a tárolójegyzék hitelesítő adatait.

Először szerezze be a tárolójegyzék bejelentkezési kiszolgálójának teljes nevét (az `<acrName>` elemet cserélje le a tárolójegyzék nevére):

```azurecli
az acr show --name <acrName> --query loginServer
```

Ezután kérdezze le a tárolójegyzék jelszavát:

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

### <a name="deploy-container"></a>A tároló üzembe helyezése

Az [az container create][az-container-create] paranccsal helyezze üzembe a tárolót. Cserélje le az `<acrLoginServer>` és `<acrPassword>` helyőrzőket az előző két parancs futtatásakor beszerzett értékekre. Cserélje le az `<acrName>` kifejezést a tárolóregisztrációs adatbázis nevére.

```azurecli
az container create --resource-group myResourceGroup --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-login-server <acrLoginServer> --registry-username <acrName> --registry-password <acrPassword> --dns-name-label aci-demo --ports 80
```

Néhány másodpercen belül meg kell kapnia az Azure kezdeti válaszát. A `--dns-name-label` értéknek egyedinek kell lennie abban az Azure-régióban, ahol a tárolópéldányt létrehozza. Módosítsa az előző parancsban szereplő értéket, ha a parancs futtatásakor **DNS-névcímke** hibaüzenetet kap.

### <a name="verify-deployment-progress"></a>Üzembe helyezés állapotának ellenőrzése

Az üzembe helyezés állapotának megtekintéséhez használja az [az container show][az-container-show] parancsot:

```azurecli
az container show --resource-group myResourceGroup --name aci-tutorial-app --query instanceView.state
```

Ismételje meg az [az container show][az-container-show] parancsot addig, amíg az állapot *Függőben* helyett *Fut* értékre nem vált, amelynek egy percen belül meg kell történnie. Ha a tároló állapota *Fut*, folytassa a következő lépéssel.

## <a name="view-the-application-and-container-logs"></a>Az alkalmazás és a tároló naplóinak megtekintése

Ha az üzembe helyezés sikeresen megtörtént, az [az container show][az-container-show] paranccsal jelenítse meg a tároló teljes tartománynevét:

```bash
az container show --resource-group myResourceGroup --name aci-tutorial-app --query ipAddress.fqdn
```

Például:
```console
$ az container show --resource-group myResourceGroup --name aci-tutorial-app --query ipAddress.fqdn
"aci-demo.eastus.azurecontainer.io"
```

A futó alkalmazás megtekintéséhez keresse meg a megjelenített DNS-nevet kedvenc böngészőjében:

![A Hello World alkalmazás a böngészőben][aci-app-browser]

Megtekintheti a tároló naplókimenetét is:

```azurecli
az container logs --resource-group myResourceGroup --name aci-tutorial-app
```

Példa a kimenetre:

```bash
$ az container logs --resource-group myResourceGroup --name aci-tutorial-app
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a jelen oktatóanyag-sorozatban létrehozott egyik erőforrásra sem, törölje az erőforráscsoportot és az azokban lévő erőforrásokat az [az group delete][az-group-delete] paranccsal. Ez a parancs törli a létrehozott tárolóregisztrációs adatbázist, valamint a futó tárolót és annak minden kapcsolódó erőforrását is.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban elvégezte a tároló üzembe helyezését az Azure Container Instancesben. A következő lépéseket hajtotta végre:

> [!div class="checklist"]
> * Üzembe helyezte az Azure Container Registryben található tárolót az Azure CLI használatával
> * Megtekintette az alkalmazást a böngészőben
> * Megtekintette a tároló naplóit

Most, hogy megismerte az alapokat, többet is megtudhat az Azure Container Instancesről, például a tárolócsoportok működéséről:

> [!div class="nextstepaction"]
> [Tárolócsoportok az Azure Container Instancesben](container-instances-container-groups.md)

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install-azure-cli
[prepare-app]: ./container-instances-tutorial-prepare-app.md
