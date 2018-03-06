---
title: "Az Azure Container Instances oktatóanyaga – Az alkalmazás előkészítése"
description: "Az Azure Container Instances oktatóanyaga, 1/3. rész – Alkalmazás előkészítése az Azure Container Instancesben való üzembe helyezéshez"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 5012412ec642a04102836274caea253635376efb
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2018
---
# <a name="create-container-for-deployment-to-azure-container-instances"></a>Tároló létrehozása az Azure Container Instances szolgáltatásban való üzembe helyezéshez

Az Azure Container Instances lehetővé teszi Docker-tárolók üzembe helyezését az Azure-infrastruktúrán anélkül, hogy ehhez virtuális gépeket kellene kiépítenie vagy magasabb szintű szolgáltatást kellene alkalmaznia. Ebben az oktatóanyagban egy kis méretű webalkalmazást hozhat létre a Node.js-ben, majd becsomagolhatja egy, az Azure Container Instances használatával futtatható tárolóba.

A cikk, amely a sorozat első része, a következő lépésekből áll:

> [!div class="checklist"]
> * Alkalmazás forráskódjának klónozása a GitHubról
> * Tárolórendszerkép létrehozása az alkalmazás forrásából
> * A rendszerkép tesztelése helyi Docker-környezetben

A következő oktatóanyagokban feltöltheti a rendszerképet egy Azure Container Registrybe, majd üzembe helyezheti Azure Container Instancesben.

## <a name="before-you-begin"></a>Előkészületek

Az oktatóanyaghoz az Azure CLI 2.0.23-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése][azure-cli-install].

Az oktatóanyag feltételezi, hogy rendelkezik a Docker fő fogalmaira, például a tárolókra, tárolórendszerképekre és az alapszintű `docker`-parancsokra vonatkozó alapvető ismeretekkel. Amennyiben szükséges, tekintse meg a tárolók alapfogalmainak ismertetését a [Docker használatának első lépései között][docker-get-started].

Az oktatóanyag elvégzéséhez szüksége lesz egy helyileg telepített Docker-fejlesztési környezetre. A Docker csomagokat biztosít, amelyekkel a Docker egyszerűen konfigurálható bármely [Mac][docker-mac], [Windows][docker-windows] vagy [Linux][docker-linux] rendszeren.

Az Azure Cloud Shell nem tartalmazza a jelen oktatóanyag lépéseinek elvégzéséhez szükséges Docker-összetevőket, ezért az oktatóanyag teljesítéséhez telepítenie kell a számítógépén az Azure CLI-t és a Docker-fejlesztési környezetet.

## <a name="get-application-code"></a>Az alkalmazáskód letöltése

Az oktatóanyagban foglalt példa egy, a [Node.js][nodejs] használatával létrehozott egyszerű webalkalmazást tartalmaz. Az alkalmazás egy statikus HTML-oldalt szolgál ki, és így néz ki:

![Az oktatóanyag alkalmazása böngészőben megjelenítve][aci-tutorial-app]

Töltse le a mintát a Git használatával:

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

## <a name="build-the-container-image"></a>Építse fel a tárolórendszerképet

A mintatárházban elérhető Docker-fájl bemutatja a tároló felépítésének menetét. Egy [hivatalos Node.js-rendszerképpel][docker-hub-nodeimage] indul, amely az [Alpine Linux][alpine-linux] rendszeren alapul – ez egy kisebb kiadás, amely jól használható a tárolókkal. Ezután bemásolja az alkalmazásfájlokat a tárolóba, telepíti a függőségeket a Node Package Manager használatával, és végül elindítja az alkalmazást.

```Dockerfile
FROM node:8.9.3-alpine
RUN mkdir -p /usr/src/app
COPY ./app/ /usr/src/app/
WORKDIR /usr/src/app
RUN npm install
CMD node /usr/src/app/index.js
```

A [docker build][docker-build] paranccsal hozza létre a tárolórendszerképet, és lássa el az *aci-tutorial-app* címkével:

```bash
docker build ./aci-helloworld -t aci-tutorial-app
```

A [docker build][docker-build] parancs kimenete a következőhöz hasonló (az olvashatóság érdekében csonkolva):

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

A [docker images][docker-images] paranccsal megtekintheti az összeállított rendszerképet:

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
> * Az alkalmazás forrásának klónozása a GitHubról
> * Tárolórendszerképek létrehozása az alkalmazás forrásából
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
