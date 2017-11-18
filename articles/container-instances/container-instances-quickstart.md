---
title: "Gyors üzembe helyezés – az első Azure tároló példányok tároló létrehozása"
description: "Az Azure Container Instances üzembe helyezése és az első lépések"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: mmacy
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/17/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: a6d5690edd9020e777f3d71c41a53856d0a400db
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2017
---
# <a name="create-your-first-container-in-azure-container-instances"></a>Az első tároló létrehozása az Azure Container Instances szolgáltatásban
Az Azure tároló példányok megkönnyíti a létrehozása és kezelése a Docker-tároló az Azure virtuális gépeket, vagy egy magasabb szintű szolgáltatást elfogadása nélkül. A gyors üzembe helyezés a tároló létrehozása az Azure-ban, és tegye elérhetővé azt az interneten, egy nyilvános IP-címmel. Ez a művelet egyetlen paranccsal hajtható végre. Néhány másodpercen belül megjelenik ez a böngészőben:

![Az Azure Container Instances használatával üzembe helyezett alkalmazás képe a böngészőben][aci-app-browser]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Az Azure-felhő rendszerhéj vagy a helyi telepítése az Azure parancssori felület segítségével a gyors üzembe helyezés befejeződik. Rendszererőforrásokra telepíti, és a parancssori felület helyileg, a gyors üzembe helyezés megköveteli, hogy futnak-e az Azure parancssori felület 2.0.20 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Azure Container Instances Azure-erőforrások, amelyeket egy Azure-erőforráscsoportba kell helyezni. Ez egy olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot a a [az csoport létrehozása] [ az-group-create] parancsot.

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Tároló létrehozása

Létrehozhat egy tárolót, adja meg a nevét, egy Docker-lemezképet, és egy Azure erőforráscsoport a [az tároló létrehozása] [ az-container-create] parancsot. Ha szeretné, közzéteheti a tárolót az interneten egy nyilvános IP-cím használatával. A gyors üzembe helyezés, telepít egy tároló, amelyen egy kis webalkalmazás írt [Node.js](http://nodejs.org).

```azurecli-interactive
az container create --name mycontainer --image microsoft/aci-helloworld --resource-group myResourceGroup --ip-address public --ports 80
```

Néhány másodperc elteltével válasz érkezik a kérésre. Kezdetben a tárolóban van a **létrehozása** állapotát, de néhány másodpercen belül kell kezdődnie. Az állapotkezelő segítségével ellenőrizheti a [az tároló megjelenítése] [ az-container-show] parancs:

```azurecli-interactive
az container show --name mycontainer --resource-group myResourceGroup
```

A kimenet alján látható a tároló kiépítési állapota és IP-címe:

```json
...
"ipAddress": {
      "ip": "13.88.176.27",
      "ports": [
        {
          "port": 80,
          "protocol": "TCP"
        }
      ]
    },
    "osType": "Linux",
    "provisioningState": "Succeeded"
...
```

Ha áthelyezi a tároló a **sikeres** állapotba kerül, a böngésző, a megadott IP-cím használatával csatlakozni tud hozzá.

![Az Azure Container Instances használatával üzembe helyezett alkalmazás képe a böngészőben][aci-app-browser]

## <a name="pull-the-container-logs"></a>A tároló naplóinak lekérése

Képes lekérni a ahhoz a tárolóhoz létrehozott naplók a [az tároló naplók] [ az-container-logs] parancs:

```azurecli-interactive
az container logs --name mycontainer --resource-group myResourceGroup
```

Kimenet:

```bash
listening on port 80
::ffff:10.240.255.105 - - [21/Jul/2017:00:01:46 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.255.105 - - [21/Jul/2017:00:01:46 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://104.210.39.122/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="delete-the-container"></a>A tároló törlése

Amikor elkészült, a tárolóval, akkor is távolítsa el azt a [az tároló törlése] [ az-container-delete] parancs:

```azurecli-interactive
az container delete --name mycontainer --resource-group myResourceGroup
```

Győződjön meg arról, hogy a tároló törölve lett, hajtsa végre a [az tároló lista](/cli/azure/container#az_container_list) parancs:

```azurecli-interactive
az container list --resource-group myResourceGroup -o table
```

A **mycontainer** tároló nem szerepelnek a parancs kimenetét. Ha nincs más tárolók erőforráscsoportban, nincs kimenet jelenik meg.

## <a name="next-steps"></a>Következő lépések

A gyors üzembe helyezés tárolójában kódjának összes rendelkezésre áll [a Githubon][app-github-repo], a Dockerfile együtt. Ha próbaképpen szeretné maga létrehozni és üzembe helyezni az Azure Container Instances szolgáltatásban az Azure Container Registry használatával, folytassa az Azure Container Instances oktatóanyagával.

> [!div class="nextstepaction"]
> [Az Azure Container Instances oktatóanyagai](./container-instances-tutorial-prepare-app.md)

Próbálja ki az Azure-on futó tárolók az orchestration rendszer beállítások, tekintse meg a [Service Fabric] [ service-fabric] vagy [Azure tároló szolgáltatás (AKS)] [ container-service] quickstarts.

<!-- LINKS -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[az-group-create]: /cli/azure/group?view=azure-cli-latest#az_group_create
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create
[az-container-delete]: /cli/azure/container?view=azure-cli-latest#az_container_delete
[az-container-list]: /cli/azure/container?view=azure-cli-latest#az_container_list
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az_container_logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show
[service-fabric]: ../service-fabric/service-fabric-quickstart-containers.md
[container-service]: ../aks/kubernetes-walkthrough.md


<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png
