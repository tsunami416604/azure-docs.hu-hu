---
title: "Azure tároló példányok útmutató – Azure tároló beállításjegyzék előkészítése"
description: "Azure tároló példányok útmutató – Azure tároló beállításjegyzék előkészítése"
services: container-instances
documentationcenter: 
author: neilpeterson
manager: timlt
editor: mmacy
tags: acs, azure-container-service
keywords: "Docker, tárolók, mikroszolgáltatások, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 0beadcca2247fb64c03835c4cb1e1e4fb9426d6f
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2017
---
# <a name="deploy-and-use-azure-container-registry"></a>Üzembe helyezés és használat Azure tároló beállításjegyzék

Ez az egy háromrészes oktatóanyag második rész. Az a [előző lépésben](container-instances-tutorial-prepare-app.md), a tároló-lemezkép létrejött egy egyszerű webes alkalmazáshoz írt [Node.js](http://nodejs.org). Ebben az oktatóanyagban a lemezkép egy Azure-tároló beállításjegyzék leküldéses. A tároló kép nem hozott létre, ha vissza [oktatóanyag 1 – tároló kép létrehozása](container-instances-tutorial-prepare-app.md).

Az Azure-tároló beállításjegyzék egy Azure-alapú, személyes beállításjegyzék Docker-tároló lemezképek. Ez az oktatóanyag végigvezeti Azure tároló beállításjegyzék-példány telepítését, valamint a végez leküldést a tároló-lemezkép. Befejeződött a lépések az alábbiak:

> [!div class="checklist"]
> * Egy Azure-tároló beállításjegyzék-példány telepítése
> * Azure-tároló beállításjegyzék címkézési tároló képe
> * Azure-tároló beállításjegyzék Rendszerkép feltöltése

A következő útmutatókból telepít a tároló a személyes beállításjegyzékből Azure tároló példányok.

## <a name="before-you-begin"></a>Előkészületek

Ez az oktatóanyag megköveteli, hogy futnak-e az Azure parancssori felület 2.0.21 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli).

Az oktatóanyag elvégzéséhez szüksége lesz egy Docker-fejlesztési környezetre. A Docker csomagokat biztosít, amelyekkel a Docker egyszerűen konfigurálható bármely [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) vagy [Linux](https://docs.docker.com/engine/installation/#supported-platforms) rendszeren.

Azure Cloud rendszerhéj nem tartalmazza a Docker-összetevők minden egyes lépéseinek befejezéséhez szükséges az oktatóanyag. Ezért ajánlott az Azure CLI és a Docker fejlesztőkörnyezet helyi telepítését.

## <a name="deploy-azure-container-registry"></a>Telepítse az Azure tároló beállításjegyzék

Egy Azure-tároló beállításjegyzék való telepítésekor, először egy erőforráscsoportot. Egy Azure-erőforráscsoportot gyűjteményei logikai mely Azure az erőforrások telepítése és kezelése.

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#create) paranccsal. Ebben a példában az erőforráscsoport neve *myResourceGroup* jön létre a *eastus* régióban.

```azurecli
az group create --name myResourceGroup --location eastus
```

Hozzon létre egy Azure-tárolót beállításjegyzéket a [az acr létrehozása](/cli/azure/acr#create) parancsot. A tároló neve **egyedinek kell lennie** Azure,-ban, és 5-50 alfanumerikus karaktereket tartalmazhat. Cserélje le `<acrName>` a beállításjegyzék egyedi nevére:

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Például hozzon létre egy Azure-tárolót beállításjegyzék nevű *mycontainerregistry082*:

```azurecli
az acr create --resource-group myResourceGroup --name mycontainerregistry082 --sku Basic --admin-enabled true
```

Ez az oktatóanyag a többi, egész használjuk `<acrName>` a tároló neve választott számára.

## <a name="container-registry-login"></a>Tároló beállításjegyzék bejelentkezés

A ACR példányát előtt képek rá kell bejelentkezni. Használja a [az acr bejelentkezési](/cli/azure/acr#az_acr_login) parancs használatával végrehajtani a műveletet. Egyedi név, a tároló beállításjegyzék létrehozásakor meg kell adnia.

```azurecli
az acr login --name <acrName>
```

A parancs visszaadja a `Login Succeeded` üzenet amint befejeződött.

## <a name="tag-container-image"></a>Címke tároló kép

A titkos beállításjegyzékből tároló lemezkép telepítéséhez, a lemezkép kell címkézését a `loginServer` nevét a beállításjegyzékben.

Lemezképek aktuális listájának megtekintéséhez használja a `docker images` parancsot.

```bash
docker images
```

Kimenet:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

Ahhoz, hogy a loginServer nevét, a következő parancsot. Cserélje le `<acrName>` a tároló beállításjegyzék nevével.

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Példa a kimenetre:

```
Result
------------------------
mycontainerregistry082.azurecr.io
```

Címke a *aci-oktatóanyag – alkalmazás* a tároló beállításjegyzék loginServer lemezkép. Továbbá adja hozzá `:v1` , a lemezkép neve végén. Ezt a címkét azt jelzi, hogy a lemezkép verziószámát. Cserélje le `<acrLoginServer>` eredményével a `az acr show` parancsot csak hajtja végre.

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

Miután megjelölve, futtassa az `docker images` ellenőrzése a műveletet.

```bash
docker images
```

Kimenet:

```bash
REPOSITORY                                                TAG                 IMAGE ID            CREATED             SIZE
aci-tutorial-app                                          latest              5c745774dfa9        39 seconds ago      68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app        v1                  a9dace4e1a17        7 minutes ago       68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>Azure-tároló beállításjegyzék leküldéses kép

Leküldéses a *aci-oktatóanyag – alkalmazás* lemezképet a beállításjegyzéket a `docker push` parancsot. Cserélje le `<acrLoginServer>` a korábbi lépésben beszerzése a teljes bejelentkezési kiszolgáló nevével.

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

A `push` művelet néhány másodpercre kell néhány percet, attól függően, hogy az internetkapcsolat, és a kimenete az alábbihoz hasonló:

```bash
The push refers to a repository [mycontainerregistry082.azurecr.io/aci-tutorial-app]
3db9cac20d49: Pushed
13f653351004: Pushed
4cd158165f4d: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:ed67fff971da47175856505585dcd92d1270c3b37543e8afd46014d328f05715 size: 1576
```

## <a name="list-images-in-azure-container-registry"></a>Azure-tároló beállításjegyzék lemezképek felsorolása

Olyan lemezképkészlet, amellyel az Azure-tárolóba beállításjegyzék értesítését listáját adja vissza, használja a [az acr tárház lista](/cli/azure/acr/repository#list) parancsot. A parancs frissíti a tároló neve.

```azurecli
az acr repository list --name <acrName> --output table
```

Kimenet:

```azurecli
Result
----------------
aci-tutorial-app
```

A címkék azokba megtekintéséhez használja a [az acr tárház megjelenítése-címkék](/cli/azure/acr/repository#show-tags) parancsot.

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

Kimenet:

```azurecli
Result
--------
v1
```

## <a name="next-steps"></a>Következő lépések

Az oktatóanyag egy Azure-tároló beállításjegyzék Azure tároló példányok való használatra készült, és a tároló kép leküldött volt. A következő lépéseket hajtotta végre:

> [!div class="checklist"]
> * Egy Azure-tároló beállításjegyzék-példány telepítése
> * A tároló-lemezkép az Azure-tároló beállításjegyzék címkézett
> * Azure tároló beállításjegyzék lemezkép feltöltése

A következő oktatóanyag az Azure-ban Azure tároló példányok a tároló telepítésével kapcsolatos további továbblépés.

> [!div class="nextstepaction"]
> [Azure-tároló példányok – tárolók üzembe helyezése](./container-instances-tutorial-deploy-app.md)
