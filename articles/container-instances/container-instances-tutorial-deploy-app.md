---
title: "Azure tároló példányok útmutató - alkalmazás központi telepítése"
description: "Azure tároló példányok útmutató - alkalmazás központi telepítése"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/26/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 3b651526f5ee3197e7d04accb6a87e2f10bf0791
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2017
---
# <a name="deploy-a-container-to-azure-container-instances"></a>A tároló üzembe Azure tároló példányokhoz

Ez az egy háromrészes az oktatóanyag utolsó. A korábbi szakaszokban [a tároló-lemezkép létrejött](container-instances-tutorial-prepare-app.md) és [egy Azure-tároló beállításjegyzék leküldött](container-instances-tutorial-prepare-acr.md). Ez a szakasz az oktatóanyag befejezése Azure tároló példányokhoz a tároló üzembe helyezésével. Befejeződött a lépések az alábbiak:

> [!div class="checklist"]
> * A tároló az Azure parancssori felület használatával Azure tároló beállításjegyzékből való telepítése
> * Az alkalmazás megtekintése a böngészőben
> * A tároló naplók megtekintése

## <a name="before-you-begin"></a>Előkészületek

Ez az oktatóanyag megköveteli, hogy futnak-e az Azure parancssori felület 2.0.20 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli).

Az oktatóanyag elvégzéséhez szüksége lesz egy Docker-fejlesztési környezetre. A Docker csomagokat biztosít, amelyekkel a Docker egyszerűen konfigurálható bármely [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) vagy [Linux](https://docs.docker.com/engine/installation/#supported-platforms) rendszeren.

Azure Cloud rendszerhéj nem tartalmazza a Docker-összetevők minden egyes lépéseinek befejezéséhez szükséges az oktatóanyag. Ezért ajánlott az Azure CLI és a Docker fejlesztőkörnyezet helyi telepítését.

## <a name="deploy-the-container-using-the-azure-cli"></a>Az Azure parancssori felület használatával tároló üzembe

Az Azure parancssori felület lehetővé teszi, hogy egy parancs egy Azure tároló példányokhoz tárolójára központi telepítését. Mivel a tároló kép a saját Azure-tároló beállításjegyzék üzemelteti, meg kell adnia az eléréséhez szükséges hitelesítő adatok. Ha szükséges, alább látható módon lekérdezheti azokat.

Tároló beállításjegyzék bejelentkezési kiszolgáló (a beállításjegyzék nevű frissítés):

```azurecli
az acr show --name <acrName> --query loginServer
```

Tároló beállításjegyzék jelszó:

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

A tároló lemezkép 1 Processzormagok erőforrás kérését és 1 GB memóriát a tároló beállításjegyzékből való telepítéséhez futtassa a következő parancsot:

```azurecli
az container create --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-password <acrPassword> --ip-address public -g myResourceGroup
```

Néhány másodpercen belül kap egy kezdeti választ az Azure Resource Manager. A központi telepítési állapotának megtekintéséhez használja:

```azurecli
az container show --name aci-tutorial-app --resource-group myResourceGroup --query state
```

A Folytatás ezt a parancsot futtatja, amíg az állapot *függőben lévő* való *futtató*. Azt is lépjen.

## <a name="view-the-application-and-container-logs"></a>Az alkalmazás és a tároló naplók megtekintése

Miután a telepítés sikeres, az IP-cím a következő parancs kimenetében megjelennek a böngészőben nyissa meg:

```bash
az container show --name aci-tutorial-app --resource-group myResourceGroup --query ipAddress.ip
```

```json
"13.88.176.27"
```

![Hello world alkalmazást a böngészőben][aci-app-browser]

A kimenet a tároló is megtekintheti:

```azurecli
az container logs --name aci-tutorial-app -g myResourceGroup
```

Kimenet:

```bash
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://13.88.176.27/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban központi telepítése a tárolók Azure tároló példányokhoz befejeződött. A következő lépéseket hajtotta végre:

> [!div class="checklist"]
> * A tároló az Azure parancssori felület használatával Azure tároló beállításjegyzékből való telepítése
> * Az alkalmazás megtekintése a böngészőben
> * A tároló naplók megtekintése

<!-- LINKS -->
[prepare-app]: ./container-instances-tutorial-prepare-app.md

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png
