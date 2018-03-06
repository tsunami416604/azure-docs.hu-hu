---
title: "Azure Container Service-oktatóanyag – Az alkalmazás előkészítése"
description: "Azure Container Service-oktatóanyag – Az alkalmazás előkészítése"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 696ba0d19aef0c550b00616d00438d081081027c
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2018
---
# <a name="create-container-images-to-be-used-with-azure-container-service"></a>Az Azure Container Service-szel használható tárolórendszerképek létrehozása

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Ebben az oktatóanyagban, amely egy hétrészes sorozat első része, egy többtárolós alkalmazást fog előkészíteni a Kubernetesben való használathoz. Ennek lépései az alábbiak:  

> [!div class="checklist"]
> * Alkalmazás forrásának klónozása a GitHubról  
> * Tárolórendszerkép létrehozása az alkalmazás forrásából
> * Az alkalmazás tesztelése helyi Docker-környezetben

Miután végeztünk ezzel, az alábbi alkalmazás elérhető lesz a helyi fejlesztői környezetben.

![Egy Azure-beli Kubernetes-fürt képe](media/container-service-kubernetes-tutorials/azure-vote.png)

Az ezt követő oktatóanyagokban a tárolórendszerképet feltölti a rendszer egy Azure Container Registrybe, majd egy Azure-ban üzemeltetett Kubernetes-fürtön futtatja.

## <a name="before-you-begin"></a>Előkészületek

Az oktatóanyag feltételezi, hogy rendelkezik a Docker fő fogalmaira, például a tárolókra, tárolórendszerképekre és az alapszintű Docker-parancsokra vonatkozó alapvető ismeretekkel. Amennyiben szükséges, tekintse meg a tárolók alapfogalmainak ismertetését a [Bevezetés a Docker használatába]( https://docs.docker.com/get-started/) című cikkben. 

Az oktatóanyag elvégzéséhez szüksége lesz egy Docker-fejlesztési környezetre. A Docker csomagokat biztosít, amelyekkel a Docker egyszerűen konfigurálható bármely [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) vagy [Linux](https://docs.docker.com/engine/installation/#supported-platforms) rendszeren.

Az Azure Cloud Shell nem tartalmazza a jelen oktatóanyag lépéseinek elvégzéséhez szükséges Docker-összetevőket, Ezért ajánlott egy teljes Docker fejlesztési környezet használata.

## <a name="get-application-code"></a>Az alkalmazáskód letöltése

A jelen oktatóanyagban használt mintaalkalmazás egy alapszintű szavazóalkalmazás. Az alkalmazás egy előtérbeli webes összetevőből és egy háttérbeli Redis-példányból áll. A webes összetevő egy egyéni tárolórendszerképbe van csomagolva. A Redis-példány a Docker Hubról származó, módosítatlan rendszerképet használ.  

A git használatával töltse le az alkalmazás egy másolatát a fejlesztői környezetbe.

```bash
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Módosítsa a könyvtárakat, hogy a klónozott könyvtárból dolgozzon.

```
cd azure-voting-app-redis
```

A könyvtárán belül található meg az alkalmazás forráskódja, egy előre létrehozott Docker Compose-fájl és egy Kubernetes-jegyzékfájl. Ezeket a fájlokat használjuk az oktatóanyagokban. 

## <a name="create-container-images"></a>Tárolórendszerképek létrehozása

A [Docker Compose](https://docs.docker.com/compose/) segítségével automatizálhatja a tárolórendszerképekből való összeállítást és a többtárolós alkalmazások üzembe helyezését.

Futtassa a `docker-compose.yml` fájlt a tárolórendszerkép létrehozásához, töltse le a Redis-rendszerképet, és indítsa el az alkalmazást.

```bash
docker-compose up -d
```

Amikor elkészült, a [docker images](https://docs.docker.com/engine/reference/commandline/images/) paranccsal tekintheti meg a létrehozott rendszerképeket.

```bash
docker images
```

Figyelje meg, hogy három rendszerkép lett letöltve vagy jött létre. Az `azure-vote-front` rendszerkép tartalmazza az alkalmazást, és a `nginx-flask` rendszerképet használja alapként. A `redis` rendszerkép használatával indítható el egy Redis-példány.

```bash
REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Futtassa a [docker ps](https://docs.docker.com/engine/reference/commandline/ps/) parancsot a futó tárolók megtekintéséhez.

```bash
docker ps
```

Kimenet:

```bash
CONTAINER ID        IMAGE             COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        azure-vote-front  "/usr/bin/supervisord"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:8080->80/tcp   azure-vote-front
b68fed4b66b6        redis             "docker-entrypoint..."   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

## <a name="test-application-locally"></a>Az alkalmazás helyi tesztelése

A böngészőben nyissa meg a http://localhost:8080 címet a futó alkalmazás megtekintéséhez.

![Egy Azure-beli Kubernetes-fürt képe](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Most, hogy az alkalmazás működésének ellenőrzése megtörtént, a futó tárolók leállíthatók és eltávolíthatók. Ne törölje a tárolórendszerképeket. Az `azure-vote-front` rendszerképet a következő oktatóanyagban töltjük fel egy Azure Container Registry-példányba.

Futtassa a következő parancsot a futó tárolók leállításához.

```bash
docker-compose stop
```

Törölje a leállított tárolókat és erőforrásokat az alábbi paranccsal.

```bash
docker-compose down
```

A művelet befejezésekor az Azure Vote alkalmazást tartalmazó tárolórendszerképet kap.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy alkalmazást teszteltünk, és tárolórendszerképeket hoztunk létre az alkalmazáshoz. A következő lépéseket hajtotta végre:

> [!div class="checklist"]
> * Az alkalmazás forrásának klónozása a GitHubról  
> * Tárolórendszerkép létrehozása az alkalmazás forrásából
> * Az alkalmazás tesztelése helyi Docker-környezetben

Folytassa a következő oktatóanyaggal, amelyben a tárolórendszerképek az Azure Container Registry-ben való tárolásának módját ismerheti meg.

> [!div class="nextstepaction"]
> [Rendszerképek leküldése az Azure Container Registry-be](./container-service-tutorial-kubernetes-prepare-acr.md)
