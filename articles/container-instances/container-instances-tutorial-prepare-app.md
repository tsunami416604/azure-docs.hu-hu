---
title: "Azure tároló példányok útmutató – az alkalmazás előkészítése"
description: "Alkalmazás előkészítése az Azure Container Instances szolgáltatásban való üzembe helyezéshez"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 11/20/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 6a168b600833c7e4544da7a5f5f4b7a0af73e0b5
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
---
# <a name="create-container-for-deployment-to-azure-container-instances"></a>Tároló létrehozása az Azure Container Instances szolgáltatásban való üzembe helyezéshez

Az Azure Container Instances lehetővé teszi Docker-tárolók üzembe helyezését az Azure-infrastruktúrán anélkül, hogy ehhez virtuális gépeket kellene kiépítenie vagy magasabb szintű szolgáltatást kellene alkalmaznia. Ebben az oktatóanyagban kis node.js webalkalmazás létrehozása, és olyan tároló, amely Azure tároló példányok használatával futtathatja a csomag azt. Az oktatóanyag a következőket ismerteti:

> [!div class="checklist"]
> * Alkalmazás forrásának klónozása a GitHubról
> * Tárolórendszerképek létrehozása az alkalmazás forrásából
> * A rendszerképek tesztelése helyi Docker-környezetben

A következő útmutatókból a Rendszerkép feltöltése az Azure-tároló beállításjegyzék, és majd központilag telepítenie kell az Azure-tároló példányok.

## <a name="before-you-begin"></a>Előkészületek

Ez az oktatóanyag megköveteli, hogy futnak-e az Azure parancssori felület 2.0.21 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli).

Ez az oktatóanyag feltételezi, hogy core alapvető ismeretekkel Docker fogalmakat, például a tárolók, a tároló lemezképeket és az alapszintű `docker` parancsok. Amennyiben szükséges, tekintse meg a tárolók alapfogalmainak ismertetését a [Bevezetés a Docker használatába]( https://docs.docker.com/get-started/) című cikkben.

Az oktatóanyag elvégzéséhez szüksége lesz egy Docker-fejlesztési környezetre. A Docker csomagokat biztosít, amelyekkel a Docker egyszerűen konfigurálható bármely [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) vagy [Linux](https://docs.docker.com/engine/installation/#supported-platforms) rendszeren.

Azure Cloud rendszerhéj nem tartalmazza a Docker-összetevők minden egyes lépéseinek befejezéséhez szükséges az oktatóanyag. Ezért ajánlott az Azure CLI és a Docker fejlesztőkörnyezet helyi telepítését.

## <a name="get-application-code"></a>Az alkalmazáskód letöltése

Az oktatóanyagban foglalt példa egy, a [Node.js](http://nodejs.org) használatával létrehozott egyszerű webalkalmazást tartalmaz. Az alkalmazás egy statikus HTML-oldalt szolgál ki, és így néz ki:

![Az oktatóanyag alkalmazása böngészőben megjelenítve][aci-tutorial-app]

Töltse le a mintát a Git használatával:

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

## <a name="build-the-container-image"></a>Építse fel a tárolórendszerképet

A mintatárházban elérhető Docker-fájl bemutatja a tároló felépítésének menetét. Egy [hivatalos Node.js rendszerképpel][dockerhub-nodeimage] indul, amely az [Alpine Linux](https://alpinelinux.org/) rendszeren alapul – ez egy kisebb kiadás, amely jól használható a tárolókkal. Ezután bemásolja az alkalmazásfájlokat a tárolóba, telepíti a függőségeket a Node Package Manager használatával, és végül elindítja az alkalmazást.

```Dockerfile
FROM node:8.2.0-alpine
RUN mkdir -p /usr/src/app
COPY ./app/* /usr/src/app/
WORKDIR /usr/src/app
RUN npm install
CMD node /usr/src/app/index.js
```

A `docker build` parancs használatával hozza létre a tárolórendszerképet, és lássa el az *aci-tutorial-app* címkével:

```bash
docker build ./aci-helloworld -t aci-tutorial-app
```

Kimenetét a `docker build` parancs (csonkolt olvashatóság érdekében) a következőhöz hasonló:

```bash
Sending build context to Docker daemon  119.3kB
Step 1/6 : FROM node:8.2.0-alpine
8.2.0-alpine: Pulling from library/node
88286f41530e: Pull complete
84f3a4bf8410: Pull complete
d0d9b2214720: Pull complete
Digest: sha256:c73277ccc763752b42bb2400d1aaecb4e3d32e3a9dbedd0e49885c71bea07354
Status: Downloaded newer image for node:8.2.0-alpine
 ---> 90f5ee24bee2
...
Step 6/6 : CMD node /usr/src/app/index.js
 ---> Running in f4a1ea099eec
 ---> 6edad76d09e9
Removing intermediate container f4a1ea099eec
Successfully built 6edad76d09e9
Successfully tagged aci-tutorial-app:latest
```

A `docker images` használatával tekintse meg a felépített rendszerképet:

```bash
docker images
```

Kimenet:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

## <a name="run-the-container-locally"></a>Futtassa helyileg a tárolót

Mielőtt megpróbálná üzembe helyezni a tárolót az Azure Container Instances szolgáltatásban, futtassa helyileg, hogy ellenőrizze a működését. A `-d` kapcsolóval a tároló a háttérben működtethető, míg a `-p` kapcsolóval leképezheti a számítási erőforrás egy tetszőleges portját a tároló 80-as portjára.

```bash
docker run -d -p 8080:80 aci-tutorial-app
```

Nyissa meg a böngészőben a http://localhost:8080 helyet, és ellenőrizze, hogy a tároló fut-e.

![Az alkalmazás helyileg történő futtatása a böngészőben][aci-tutorial-app-local]

## <a name="next-steps"></a>Következő lépések

Az oktatóanyagban egy, az Azure Container Instances szolgáltatásban üzembe helyezhető tárolórendszerképet hozott létre. A következő lépéseket hajtotta végre:

> [!div class="checklist"]
> * Az alkalmazás forráskódjának a Githubról klónozása
> * Az alkalmazás forrás létrehozott tároló lemezképek
> * A tároló helyi tesztelése

Folytassa a következő oktatóanyaggal, amelyben a tárolórendszerképek az Azure Container Registry-ben való tárolásának módját ismerheti meg.

> [!div class="nextstepaction"]
> [Rendszerképek leküldése az Azure Container Registry-be](./container-instances-tutorial-prepare-acr.md)

<!-- LINKS -->
[dockerhub-nodeimage]: https://store.docker.com/images/node

<!--- IMAGES --->
[aci-tutorial-app]:./media/container-instances-quickstart/aci-app-browser.png
[aci-tutorial-app-local]: ./media/container-instances-tutorial-prepare-app/aci-app-browser-local.png