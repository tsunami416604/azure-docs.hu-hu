---
title: "Az első Azure Container Instances-tároló létrehozása | Azure Docs"
description: "Az Azure Container Instances üzembe helyezése és az első lépések"
services: container-service
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: 
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2017
ms.author: seanmck
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: 3b15d6645b988f69f1f05b27aff6f726f34786fc
ms.openlocfilehash: 933299ce5a5d6f5b2262d40ae768019ccaf8796a
ms.contentlocale: hu-hu
ms.lasthandoff: 07/26/2017

---

# <a name="create-your-first-container-in-azure-container-instances"></a>Az első tároló létrehozása az Azure Container Instances szolgáltatásban

Az Azure Container Instances segítségével egyszerűen hozhat létre és felügyelhet tárolókat az Azure-ban. Ebben a rövid útmutatóban létrehozhat egy tárolót az Azure-ban, és közzéteheti az interneten egy nyilvános IP-címen keresztül. Ez a művelet egyetlen paranccsal hajtható végre. Néhány másodperc elteltével a következőt láthatja a böngészőben:

![Az Azure Container Instances használatával üzembe helyezett alkalmazás képe a böngészőben][aci-app-browser]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Az Azure Container Instances parancssori felületének parancsai jelenleg csak az Azure Cloud Shellben érhetőek el.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Azure Container Instances Azure-erőforrások, amelyeket egy Azure-erőforráscsoportba kell helyezni. Ez egy olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#create) paranccsal. 

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Tároló létrehozása

A tároló létrehozásához meg kell adnia egy nevet, egy Docker-rendszerképet és egy Azure-erőforráscsoportot. Ha szeretné, közzéteheti a tárolót az interneten egy nyilvános IP-cím használatával. Ebben az esetben egy, a [Node.js](http://nodejs.org) használatával létrehozott, nagyon egyszerű webalkalmazást tartalmazó tárolót használunk.

Az Azure Container Instances parancssori felületének parancsai jelenleg csak az Azure Cloud Shellben érhetőek el.

```azurecli-interactive
az container create --name mycontainer --image microsoft/aci-helloworld --resource-group myResourceGroup --ip-address public 
```

Néhány másodperc elteltével válasz érkezik a kérésre. A tároló kezdetben **Létrehozás** állapotban lesz, de néhány másodpercen belül el kell indulnia. Az állapotot a `show` paranccsal ellenőrizheti:

```azurecli-interactive
az container show --name mycontainer --resource-group myResourceGroup
```

A kimenet alján látható a tároló kiépítési állapota és IP-címe:

```json
...
"ipAddress": {
      "ip": "13.88.8.148",
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

Miután a tároló átvált a **Sikeres** állapotba, elérhetővé válik a böngészőben a megadott IP-címen. 

![Az Azure Container Instances használatával üzembe helyezett alkalmazás képe a böngészőben][aci-app-browser]

## <a name="pull-the-container-logs"></a>A tároló naplóinak lekérése

A létrehozott tároló naplóit a `logs` paranccsal kérheti le:

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

Miután végzett a tárolóval, a `delete` parancs használatával távolíthatja el:

```azurecli-interactive
az container delete --name mycontainer --resource-group myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

A jelen rövid útmutatóban alkalmazott tároló kódja a Docker-fájljával egyetemben elérhető [a GitHubon][app-github-repo]. Ha próbaképpen szeretné maga létrehozni és üzembe helyezni az Azure Container Instances szolgáltatásban az Azure Container Registry használatával, folytassa az Azure Container Instances oktatóanyagával.

> [!div class="nextstepaction"]
> [Az Azure Container Instances oktatóanyagai](./container-instances-tutorial-prepare-app.md)


<!-- LINKS -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png
