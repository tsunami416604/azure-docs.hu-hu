---
title: "Az Azure Container Instances oktatóanyaga – Alkalmazás üzembe helyezése"
description: "Az Azure Container Instances oktatóanyaga, 3/3. rész – Alkalmazás üzembe helyezése"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 99bd03bf4c3ca2d7b1ced51ebfe8be669f271c1c
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2018
---
# <a name="deploy-a-container-to-azure-container-instances"></a>Tároló üzembe helyezése az Azure Container Instances szolgáltatásban

Ez az egy háromrészes sorozat utolsó oktatóanyaga. Az előző oktatóanyagokban [létrehozott egy tárolórendszerképet](container-instances-tutorial-prepare-app.md), és [elküldte azt egy Azure Container Registry-példányra](container-instances-tutorial-prepare-acr.md). Az oktatóanyag befejező cikke a tároló üzembe helyezését ismerteti az Azure Container Instances szolgáltatásban.

Ebben az oktatóanyagban az alábbiakat végezte el:

> [!div class="checklist"]
> * Üzembe helyezheti az Azure Container Registry-tárolót az Azure CLI használatával
> * Megtekintheti az alkalmazást a böngészőben
> * Megtekintheti a tároló naplóit

## <a name="before-you-begin"></a>Előkészületek

Az oktatóanyag elvégzéséhez az Azure CLI 2.0.27-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése][azure-cli-install].

Az oktatóanyag elvégzéséhez szüksége lesz egy helyileg telepített Docker-fejlesztési környezetre. A Docker csomagokat biztosít, amelyekkel a Docker egyszerűen konfigurálható bármely [Mac][docker-mac], [Windows][docker-windows] vagy [Linux][docker-linux] rendszeren.

Az Azure Cloud Shell nem tartalmazza a jelen oktatóanyag lépéseinek elvégzéséhez szükséges Docker-összetevőket, ezért az oktatóanyag teljesítéséhez telepítenie kell a számítógépén az Azure CLI-t és a Docker-fejlesztési környezetet.

## <a name="deploy-the-container-using-the-azure-cli"></a>Tároló üzembe helyezése az Azure CLI használatával

Az Azure CLI segítségével a tároló Azure Container Instances szolgáltatásban történő üzembe helyezése egyetlen paranccsal elvégezhető. Mivel a tároló rendszerképét a privát Azure Container Registry-példány kezeli, annak megnyitásához meg kell adnia a hitelesítő adatait. A hitelesítő adatok beszerzése az alábbi Azure CLI-parancsokkal történik.

A tárolóregisztrációs adatbázis bejelentkezési kiszolgálója (frissítse a beállításjegyzék nevével):

```azurecli
az acr show --name <acrName> --query loginServer
```

A tárolóregisztrációs adatbázis jelszava:

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

Futtassa az alábbi parancsot a tároló rendszerképének üzembe helyezéséhez a tárolóregisztrációs adatbázisból egy 1 processzormag és 1 GB memória erőforrásra vonatkozó kérelemmel. Cserélje le az `<acrLoginServer>` és `<acrPassword>` helyőrzőket az előző két parancs futtatásakor beszerzett értékekre. Cserélje le az `<acrName>` kifejezést a tárolóregisztrációs adatbázis nevére.

```azurecli
az container create --resource-group myResourceGroup --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-username <acrName> --registry-password <acrPassword> --dns-name-label aci-demo --ports 80
```

Néhány másodpercen belül meg kell kapnia az Azure Resource Manager kezdeti válaszát. A `--dns-name-label` értéknek egyedinek kell lennie abban az Azure-régióban, ahol a tárolópéldányt létrehozza. Frissítse az előző példában szereplő értéket, ha a parancs futtatásakor **DNS-név címke** hibaüzenetet kap.

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

Példa a kimenetre: `"aci-demo.eastus.azurecontainer.io"`

A futó alkalmazás megtekintéséhez keresse meg a megjelenített DNS-nevet kedvenc böngészőjében:

![A Hello World alkalmazás a böngészőben][aci-app-browser]

Megtekintheti a tároló naplókimenetét is:

```azurecli
az container logs --resource-group myResourceGroup --name aci-tutorial-app
```

Kimenet:

```bash
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://13.88.176.27/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a jelen oktatóanyag-sorozatban létrehozott egyik erőforrásra sem, törölje az erőforráscsoportot és az azokban lévő erőforrásokat az [az group delete][az-group-delete] paranccsal. Ez a parancs törli a létrehozott tárolóregisztrációs adatbázist, valamint a futó tárolót és annak minden kapcsolódó erőforrását is.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban elvégezte a tárolók üzembe helyezését az Azure Container Instances szolgáltatásban. A következő lépéseket hajtotta végre:

> [!div class="checklist"]
> * Üzembe helyezte az Azure Container Registry-tárolót az Azure CLI használatával
> * Megtekintette az alkalmazást a böngészőben
> * Megtekintette a tároló naplóit

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
[az-container-show]: /cli/azure/container#az_container_show
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install-azure-cli
[prepare-app]: ./container-instances-tutorial-prepare-app.md
