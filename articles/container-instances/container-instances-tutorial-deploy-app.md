---
title: "Azure tároló példányok útmutató - alkalmazás központi telepítése"
description: "Azure tároló példányok útmutató - alkalmazás központi telepítése"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: mmacy
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 5222c43fe381c93527ec6c8b927d723f75dd58f9
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2017
---
# <a name="deploy-a-container-to-azure-container-instances"></a>A tároló üzembe Azure tároló példányokhoz

Ez az egy háromrészes az oktatóanyag utolsó. A korábbi szakaszokban [a tároló-lemezkép létrejött](container-instances-tutorial-prepare-app.md) és [egy Azure-tároló beállításjegyzék leküldött](container-instances-tutorial-prepare-acr.md). Ez a szakasz az oktatóanyag befejezése Azure tároló példányokhoz a tároló üzembe helyezésével. Befejeződött a lépések az alábbiak:

> [!div class="checklist"]
> * A tároló az Azure parancssori felület használatával Azure tároló beállításjegyzékből való telepítése
> * Az alkalmazás megtekintése a böngészőben
> * A tároló naplók megtekintése

## <a name="before-you-begin"></a>Előkészületek

Ez az oktatóanyag megköveteli, hogy futnak-e az Azure parancssori felület 2.0.21 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli).

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

A tároló lemezkép 1 Processzormagok erőforrás kérését és 1 GB memóriát a tároló beállításjegyzékből való telepítéséhez futtassa a következő parancsot. Cserélje le `<acrLoginServer>` és `<acrPassword>` az előző két parancsot kapott értékekkel.

```azurecli
az container create --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-password <acrPassword> --ip-address public --ports 80 -g myResourceGroup
```

Néhány másodpercen belül kap egy kezdeti választ az Azure Resource Manager. A központi telepítési állapotának megtekintéséhez használja [az tároló megjelenítése](/cli/azure/container#az_container_show):

```azurecli
az container show --name aci-tutorial-app --resource-group myResourceGroup --query instanceView.state
```

Ismételje meg a `az container show` parancs, amíg az állapot *függőben lévő* való *futtató*, amely kell vennie egy perc alatt. A tároló esetén *futtató*, folytassa a következő lépéssel.

## <a name="view-the-application-and-container-logs"></a>Az alkalmazás és a tároló naplók megtekintése

Miután a telepítés sikeres, a tároló nyilvános IP-cím megjelenítése a [az tároló megjelenítése](/cli/azure/container#az_container_show) parancs:

```bash
az container show --name aci-tutorial-app --resource-group myResourceGroup --query ipAddress.ip
```

Példa a kimenetre:`"13.88.176.27"`

A futó alkalmazás megtekintéséhez navigáljon a nyilvános IP-cím, kedvenc böngészőjével.

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

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforrásokat, az oktatóanyag adatsorozat létrehozott, Ön is végrehajthatja a [az csoport törlése](/cli/azure/group#delete) parancs beírásával távolítsa el az erőforráscsoportot és a benne található összes erőforrást. Ez a parancs törli a tárolót beállításjegyzék hozott létre, valamint a futó tároló és az összes kapcsolódó erőforrások.

```azurecli-interactive
az group delete --name myResourceGroup
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
