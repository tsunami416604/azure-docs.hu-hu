---
title: "Azure Container Instances oktatóanyag – Az alkalmazás előkészítése | Azure Docs"
description: "Alkalmazás előkészítése az Azure Container Instances szolgáltatásban való üzembe helyezéshez"
services: container-instances
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
ms.date: 08/01/2017
ms.author: seanmck
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: 07ad1a6edbcb4d6160b37b4923586e23058f3c04
ms.contentlocale: hu-hu
ms.lasthandoff: 08/02/2017

---

# <a name="create-container-for-deployment-to-azure-container-instances"></a>Tároló létrehozása az Azure Container Instances szolgáltatásban való üzembe helyezéshez

Az Azure Container Instances lehetővé teszi Docker-tárolók üzembe helyezését az Azure-infrastruktúrán anélkül, hogy ehhez virtuális gépeket kellene kiépítenie vagy magasabb szintű szolgáltatást kellene alkalmaznia. Jelen oktatóanyagban egy egyszerű webalkalmazást hozhat létre a Node.js szolgáltatásban, majd becsomagolhatja azt egy, az Azure Container Instances használatával futtatható tárolóba. Az oktatóanyag a következőket ismerteti:

> [!div class="checklist"]
> * Alkalmazás forrásának klónozása a GitHubról  
> * Tárolórendszerképek létrehozása az alkalmazás forrásából
> * A rendszerképek tesztelése helyi Docker-környezetben

A következő oktatóanyagokban feltöltheti majd a rendszerképet egy Azure Container Registry jegyzékbe, majd üzembe helyezheti az Azure Container Instances szolgáltatásban.

## <a name="before-you-begin"></a>Előkészületek

Az oktatóanyag feltételezi, hogy rendelkezik a Docker fő fogalmaira, például a tárolókra, tárolórendszerképekre és az alapszintű Docker-parancsokra vonatkozó alapvető ismeretekkel. Amennyiben szükséges, tekintse meg a tárolók alapfogalmainak ismertetését a [Bevezetés a Docker használatába]( https://docs.docker.com/get-started/) című cikkben. 

Az oktatóanyag elvégzéséhez szüksége lesz egy Docker-fejlesztési környezetre. A Docker csomagokat biztosít, amelyekkel a Docker egyszerűen konfigurálható bármely [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) vagy [Linux](https://docs.docker.com/engine/installation/#supported-platforms) rendszeren.

## <a name="get-application-code"></a>Az alkalmazáskód letöltése

Az oktatóanyagban foglalt példa egy, a [Node.js](http://nodejs.org) használatával létrehozott egyszerű webalkalmazást tartalmaz. Az alkalmazás egy statikus HTML-oldalt szolgál ki, és így néz ki:

![Az oktatóanyag alkalmazása böngészőben megjelenítve][aci-tutorial-app]

Töltse le a mintát a Git használatával:

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

## <a name="build-the-container-image"></a>Építse fel a tárolórendszerképet

A mintatárházban elérhető Docker-fájl bemutatja a tároló felépítésének menetét. Egy [hivatalos Node.js rendszerképpel][dockerhub-nodeimage] indul, amely az [Alpine Linux](https://alpinelinux.org/) rendszeren alapul – ez egy kisebb kiadás, amely jól használható a tárolókkal. Ezután bemásolja az alkalmazásfájlokat a tárolóba, telepíti a függőségeket a Node Package Manager használatával, és végül elindítja az alkalmazást.

```
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
> * Az alkalmazás forrásának klónozása a GitHubról  
> * Tárolórendszerképek létrehozása az alkalmazás forrásából
> * A tároló helyi tesztelése

Folytassa a következő oktatóanyaggal, amelyben a tárolórendszerképek az Azure Container Registry-ben való tárolásának módját ismerheti meg.

> [!div class="nextstepaction"]
> [Rendszerképek leküldése az Azure Container Registry-be](./container-instances-tutorial-prepare-acr.md)

<!-- LINKS -->
[dockerhub-nodeimage]: https://hub.docker.com/r/library/node/tags/8.2.0-alpine/

<!--- IMAGES --->
[aci-tutorial-app]:./media/container-instances-quickstart/aci-app-browser.png
[aci-tutorial-app-local]: ./media/container-instances-tutorial-prepare-app/aci-app-browser-local.png
