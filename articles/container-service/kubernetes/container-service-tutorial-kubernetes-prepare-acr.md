---
title: "Azure Tárolószolgáltatás útmutató - ACR előkészítése |} Microsoft Docs"
description: "Azure Tárolószolgáltatás útmutató - ACR előkészítése"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, tárolók, mikroszolgáltatások, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 4092bfe77f09c24d9f82138bcf361da4e57a9508
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="deploy-and-use-azure-container-registry"></a>Üzembe helyezés és használat Azure tároló beállításjegyzék

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Az Azure tároló beállításjegyzék (ACR) egy Azure-alapú, személyes beállításjegyzék Docker-tároló lemezképek. Ez az oktatóanyag, része két hét, végigvezeti Azure tároló beállításjegyzék-példány telepítését, valamint a végez leküldést a tároló-lemezkép. Befejeződött a lépések az alábbiak:

> [!div class="checklist"]
> * Egy Azure tároló beállításjegyzék (ACR) példány telepítése
> * A tároló lemezkép ACR a címkézés
> * ACR a Rendszerkép feltöltése

A következő útmutatókból adott ACR példány integrálva van az Azure-tároló szolgáltatás Kubernetes fürtöt. 

## <a name="before-you-begin"></a>Előkészületek

Az a [az oktatóanyag előző](./container-service-tutorial-kubernetes-prepare-app.md), a tároló-lemezkép létrejött egy egyszerű Azure szavazás alkalmazáshoz. Ha nem hozott létre az Azure szavazás alkalmazás lemezképét, térjen vissza [oktatóanyag 1 – létrehozás tároló képek](./container-service-tutorial-kubernetes-prepare-app.md).

Ez az oktatóanyag megköveteli, hogy futnak-e az Azure parancssori felület 2.0.4 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="deploy-azure-container-registry"></a>Telepítse az Azure tároló beállításjegyzék

Egy Azure-tároló beállításjegyzék való telepítésekor, először egy erőforráscsoportot. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#create) paranccsal. Ebben a példában az erőforráscsoport neve `myResourceGroup` jön létre a `westeurope` régióban.

```azurecli
az group create --name myResourceGroup --location westeurope
```

Hozzon létre egy Azure-tárolóba beállításjegyzéket a [az acr létrehozása](/cli/azure/acr#create) parancsot. Egy tároló beállításjegyzék neve **egyedinek kell lennie**.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Ez az oktatóanyag a többi, egész használjuk `<acrname>` számára a tároló neve.

## <a name="container-registry-login"></a>Tároló beállításjegyzék bejelentkezés

Használja a [az acr bejelentkezési](https://docs.microsoft.com/en-us/cli/azure/acr#az_acr_login) próbál bejelentkezni az ACR példány parancsot. Meg kell adnia az egyedi név, a tároló beállításjegyzék létrehozásakor.

```azurecli
az acr login --name <acrName>
```

A parancs visszaadja a "Sikeres bejelentkezés" üzenet, amint befejeződött.

## <a name="tag-container-images"></a>Címke tároló lemezképek

Lemezképek aktuális listájának megtekintéséhez használja a [docker képek](https://docs.docker.com/engine/reference/commandline/images/) parancsot.

```bash
docker images
```

Kimenet:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front             latest              4675398c9172        13 minutes ago      694MB
redis                        latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        9 months ago        694MB
```

Minden egyes tároló lemezképet kell címkézését a beállításjegyzék loginServer nevét. Ezt a címkét tároló képek terjesztése egy kép beállításjegyzék műveletterv szolgál.

Ahhoz, hogy a loginServer nevét, a következő parancsot.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Most, címkét a `azure-vote-front` a tároló beállításjegyzék loginServer lemezkép. Továbbá adja hozzá `:redis-v1` , a lemezkép neve végén. Ezt a címkét azt jelzi, hogy a lemezkép verziója.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:redis-v1
```

Miután megjelölve, futtassa az [docker képek] (https://docs.docker.com/engine/reference/commandline/images/) a művelet megerősítéséhez.

```bash
docker images
```

Kimenet:

```bash
REPOSITORY                                           TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest              eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry082.azurecr.io/azure-vote-front   redis-v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask               788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-registry"></a>Leküldéses képek beállításjegyzék

Leküldéses a `azure-vote-front` kép a beállításjegyzékhez. 

Az alábbi példa használatával, cserélje le a környezetből loginServer loginServer ACR neve.

```bash
docker push <acrLoginServer>/azure-vote-front:redis-v1
```

Ez a néhány percet vesz igénybe.

## <a name="list-images-in-registry"></a>A beállításjegyzékben lemezképek felsorolása

Olyan lemezképkészlet, amellyel az Azure-tárolóba beállításjegyzék értesítését listájához való visszatéréshez felhasználói a [az acr tárház lista](/cli/azure/acr/repository#list) parancsot. A parancs frissíti az ACR-példány neve.

```azurecli
az acr repository list --name <acrName> --output table
```

Kimenet:

```azurecli
Result
----------------
azure-vote-front
```

A címkék azokba megtekintéséhez használja a [az acr tárház megjelenítése-címkék](/cli/azure/acr/repository#show-tags) parancsot.

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

Kimenet:

```azurecli
Result
--------
redis-v1
```

Oktatóanyag befejezése után a tároló kép rendelkezik lett tárolja, a saját Azure tároló beállításjegyzék-példányban. Ez a rendszerkép a rendszer az ACR Kubernetes fürthöz a következő útmutatókból.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban az Azure-tároló beállításjegyzék ACS Kubernetes fürt használatra lett előkészítve. A következő lépéseket hajtotta végre:

> [!div class="checklist"]
> * Egy Azure-tároló beállításjegyzék-példány telepítése
> * A tároló-lemezkép az ACR címkézett
> * A kép feltöltött ACR

A következő oktatóanyag az Azure-ban Kubernetes fürt telepítésével kapcsolatos további továbblépés.

> [!div class="nextstepaction"]
> [Kubernetes fürt központi telepítése](./container-service-tutorial-kubernetes-deploy-cluster.md)