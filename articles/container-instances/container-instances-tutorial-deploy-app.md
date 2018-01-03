---
title: "Azure tároló példányok útmutató - alkalmazás központi telepítése"
description: "Azure tároló példányok útmutató 3. 3. rész - alkalmazás központi telepítése"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 471caa1b24dc7017c70782c072b2068f9635244b
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2018
---
# <a name="deploy-a-container-to-azure-container-instances"></a>A tároló üzembe Azure tároló példányokhoz

Ez az egy háromrészes sorozat végső oktatóanyaga. Az adatsorozat korábbi [a tároló-lemezkép létrejött](container-instances-tutorial-prepare-app.md) és [egy Azure-tároló beállításjegyzék leküldött](container-instances-tutorial-prepare-acr.md). Ez a cikk az oktatóanyag adatsorozat Azure tároló példányokhoz a tároló üzembe helyezésével befejeződik.

Ebben az oktatóanyagban az alábbiakat végezte el:

> [!div class="checklist"]
> * A tároló az Azure parancssori felület használatával Azure tároló beállításjegyzékből való központi telepítése
> * Az alkalmazás megtekintése a böngészőben
> * A tároló naplók megtekintése

## <a name="before-you-begin"></a>Előkészületek

Ez az oktatóanyag megköveteli, hogy futnak-e az Azure parancssori felület 2.0.23 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha szeretné telepíteni vagy frissíteni, lásd: [Azure CLI 2.0 telepítése][azure-cli-install].

Az oktatóanyag elvégzéséhez egy Docker fejlesztőkörnyezet helyileg telepíteni kell. Docker biztosít, amely egyszerű konfigurálását a Docker bármely csomagok [Mac][docker-mac], [Windows][docker-windows], vagy [Linux] [ docker-linux] rendszer.

Azure Cloud rendszerhéj nem tartalmazza a Docker-összetevők minden egyes lépéseinek befejezéséhez szükséges az oktatóanyag. Az oktatóanyag teljesítéséhez a helyi számítógépen telepítenie kell az Azure CLI és a Docker fejlesztési környezet.

## <a name="deploy-the-container-using-the-azure-cli"></a>Az Azure parancssori felület használatával tároló üzembe

Az Azure parancssori felület lehetővé teszi, hogy egy parancs egy Azure tároló példányokhoz tárolójára központi telepítését. Mivel a tároló kép a saját Azure-tároló beállításjegyzék üzemelteti, meg kell adnia az eléréséhez szükséges hitelesítő adatok. Szerezze be a hitelesítő adatokat a következő Azure CLI-parancsokkal.

Tároló beállításjegyzék bejelentkezési kiszolgáló (a beállításjegyzék nevű frissítés):

```azurecli
az acr show --name <acrName> --query loginServer
```

Tároló beállításjegyzék jelszó:

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

A tároló lemezkép 1 Processzormagok erőforrás kérését és 1 GB memóriát a tároló beállításjegyzékből való telepítéséhez futtassa a következő parancsot. Cserélje le `<acrLoginServer>` és `<acrPassword>` az előző két parancsot kapott értékekkel.

```azurecli
az container create --resource-group myResourceGroup --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-password <acrPassword> --ip-address public --ports 80
```

Néhány másodpercen belül kap egy kezdeti választ az Azure Resource Manager. A központi telepítési állapotának megtekintéséhez használja [az tároló megjelenítése][az-container-show]:

```azurecli
az container show --resource-group myResourceGroup --name aci-tutorial-app --query instanceView.state
```

Ismételje meg a [az tároló megjelenítése] [ az-container-show] parancs, amíg az állapot *függőben lévő* való *futtató*, amely kell vennie egy perc alatt. A tároló esetén *futtató*, folytassa a következő lépéssel.

## <a name="view-the-application-and-container-logs"></a>Az alkalmazás és a tároló naplók megtekintése

Miután a telepítés sikeres, a tároló nyilvános IP-cím megjelenítése a [az tároló megjelenítése] [ az-container-show] parancs:

```bash
az container show --resource-group myResourceGroup --name aci-tutorial-app --query ipAddress.ip
```

Példa a kimenetre:`"13.88.176.27"`

A futó alkalmazás megtekintéséhez navigáljon a nyilvános IP-cím, kedvenc böngészőjével.

![Hello world alkalmazást a böngészőben][aci-app-browser]

A kimenet a tároló is megtekintheti:

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

Ha már nincs szüksége az erőforrásokat, az oktatóanyag adatsorozat létrehozott, Ön is végrehajthatja a [az csoport törlése] [ az-group-delete] parancs beírásával távolítsa el az erőforráscsoportot és a benne található összes erőforrást. Ez a parancs törli a tárolót beállításjegyzék hozott létre, valamint a futó tároló és az összes kapcsolódó erőforrások.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban központi telepítése a tárolók Azure tároló példányokhoz befejeződött. A következő lépéseket hajtotta végre:

> [!div class="checklist"]
> * A tároló az Azure parancssori felület használatával Azure tároló beállításjegyzékből való telepítése
> * Az alkalmazás böngészőben
> * A tároló naplók tekinthetők

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