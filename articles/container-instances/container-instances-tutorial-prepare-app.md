---
title: "Azure tároló példányok útmutató – az alkalmazás előkészítése"
description: "Azure tároló példányok útmutató 1. rész 3 – az alkalmazás Azure tároló példányokhoz üzembe helyezésének előkészítése"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: fc16be80e776d1472be775fa32354ba157d16545
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2018
---
# <a name="create-container-for-deployment-to-azure-container-instances"></a>Tároló létrehozása az Azure Container Instances szolgáltatásban való üzembe helyezéshez

Az Azure Container Instances lehetővé teszi Docker-tárolók üzembe helyezését az Azure-infrastruktúrán anélkül, hogy ehhez virtuális gépeket kellene kiépítenie vagy magasabb szintű szolgáltatást kellene alkalmaznia. Ebben az oktatóanyagban kis node.js webalkalmazás létrehozása, és olyan tároló, amely Azure tároló példányok használatával futtathatja a csomag azt.

Ebben a cikkben az adatsorozat egyik része meg:

> [!div class="checklist"]
> * A Githubból az alkalmazás forráskódjához klónozása
> * Az alkalmazás forrás tároló lemezkép létrehozása
> * A lemezkép tesztelése egy helyi Docker-környezetben

A következő útmutatókból a Rendszerkép feltöltése az Azure-tároló beállításjegyzék, és majd központilag telepítenie kell az Azure-tároló példányok.

## <a name="before-you-begin"></a>Előkészületek

Ez az oktatóanyag megköveteli, hogy futnak-e az Azure parancssori felület 2.0.23 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha szeretné telepíteni vagy frissíteni, lásd: [Azure CLI 2.0 telepítése][azure-cli-install].

Ez az oktatóanyag feltételezi, hogy core alapvető ismeretekkel Docker fogalmakat, például a tárolók, a tároló lemezképeket és az alapszintű `docker` parancsok. Ha szükséges, lásd: [Ismerkedés a Docker] [ docker-get-started] a tároló alapjai a egy ismertetése.

Az oktatóanyag elvégzéséhez egy Docker fejlesztőkörnyezet helyileg telepíteni kell. Docker biztosít, amely egyszerű konfigurálását a Docker bármely csomagok [Mac][docker-mac], [Windows][docker-windows], vagy [Linux] [ docker-linux] rendszer.

Azure Cloud rendszerhéj nem tartalmazza a Docker-összetevők minden egyes lépéseinek befejezéséhez szükséges az oktatóanyag. Az oktatóanyag teljesítéséhez a helyi számítógépen telepítenie kell az Azure CLI és a Docker fejlesztési környezet.

## <a name="get-application-code"></a>Az alkalmazáskód letöltése

A minta ebben az oktatóanyagban egy egyszerű webalkalmazást a beépített tartalmaz [Node.js][nodejs]. Az alkalmazás egy statikus HTML-oldalt szolgál ki, és így néz ki:

![Az oktatóanyag alkalmazása böngészőben megjelenítve][aci-tutorial-app]

Töltse le a mintát a Git használatával:

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

## <a name="build-the-container-image"></a>Építse fel a tárolórendszerképet

A mintatárházban elérhető Docker-fájl bemutatja a tároló felépítésének menetét. A elindul egy [hivatalos Node.js kép] [ docker-hub-nodeimage] alapján [Alpine Linux][alpine-linux], használandó kiválóan alkalmas kis terjesztési tárolók. Ezután bemásolja az alkalmazásfájlokat a tárolóba, telepíti a függőségeket a Node Package Manager használatával, és végül elindítja az alkalmazást.

```Dockerfile
FROM node:8.9.3-alpine
RUN mkdir -p /usr/src/app
COPY ./app/* /usr/src/app/
WORKDIR /usr/src/app
RUN npm install
CMD node /usr/src/app/index.js
```

Használja a [docker build] [ docker-build] parancs azt címkézés tároló lemezkép létrehozásához *aci-oktatóanyag – alkalmazás*:

```bash
docker build ./aci-helloworld -t aci-tutorial-app
```

Kimenetét a [docker build] [ docker-build] parancs (csonkolt olvashatóság érdekében) a következőhöz hasonló:

```bash
Sending build context to Docker daemon  119.3kB
Step 1/6 : FROM node:8.9.3-alpine
8.9.3-alpine: Pulling from library/node
88286f41530e: Pull complete
84f3a4bf8410: Pull complete
d0d9b2214720: Pull complete
Digest: sha256:c73277ccc763752b42bb2400d1aaecb4e3d32e3a9dbedd0e49885c71bea07354
Status: Downloaded newer image for node:8.9.3-alpine
 ---> 90f5ee24bee2
...
Step 6/6 : CMD node /usr/src/app/index.js
 ---> Running in f4a1ea099eec
 ---> 6edad76d09e9
Removing intermediate container f4a1ea099eec
Successfully built 6edad76d09e9
Successfully tagged aci-tutorial-app:latest
```

Használja a [docker képek] [ docker-images] parancs a beépített kép megtekintéséhez:

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

## <a name="next-steps"></a>További lépések

Az oktatóanyagban egy, az Azure Container Instances szolgáltatásban üzembe helyezhető tárolórendszerképet hozott létre. A következő lépéseket hajtotta végre:

> [!div class="checklist"]
> * Az alkalmazás forráskódjának a Githubról klónozása
> * Az alkalmazás forrás létrehozott tároló lemezképek
> * A tároló helyi tesztelése

Folytassa a következő oktatóanyaggal, amelyben a tárolórendszerképek az Azure Container Registry-ben való tárolásának módját ismerheti meg.

> [!div class="nextstepaction"]
> [Rendszerképek leküldése az Azure Container Registry-be](./container-instances-tutorial-prepare-acr.md)

<!--- IMAGES --->
[aci-tutorial-app]:./media/container-instances-quickstart/aci-app-browser.png
[aci-tutorial-app-local]: ./media/container-instances-tutorial-prepare-app/aci-app-browser-local.png

<!-- LINKS - External -->
[alpine-linux]: https://alpinelinux.org/
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: http://nodejs.org

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
