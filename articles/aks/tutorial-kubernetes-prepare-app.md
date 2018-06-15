---
title: Az Azure-on futó Kubernetes oktatóanyaga – Az alkalmazás előkészítés
description: AKS-oktatóanyag – Az alkalmazás előkészítése
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 50c302ddc7bad9cd2de666c1b99d1fbc6d5a62a8
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33934114"
---
# <a name="tutorial-prepare-application-for-azure-kubernetes-service-aks"></a>Oktatóanyag: Alkalmazás előkészítése az Azure Kubernetes Service (AKS) szolgáltatáshoz

Ebben az oktatóanyagban, amely egy nyolcrészes sorozat első része, egy többtárolós alkalmazást fog előkészíteni a Kubernetesben való használathoz. Ennek lépései az alábbiak:

> [!div class="checklist"]
> * Alkalmazás forrásának klónozása a GitHubról
> * Tárolórendszerkép létrehozása az alkalmazás forrásából
> * Az alkalmazás tesztelése helyi Docker-környezetben

Miután végeztünk ezzel, az alábbi alkalmazás elérhető lesz a helyi fejlesztői környezetben.

![Egy Azure-beli Kubernetes-fürt képe](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

Az ezt követő oktatóanyagokban a tárolórendszerképet feltölti a rendszer egy Azure Container Registrybe, majd egy AKS-fürtön futtatja.

## <a name="before-you-begin"></a>Előkészületek

Az oktatóanyag feltételezi, hogy rendelkezik a Docker fő fogalmaira, például a tárolókra, tárolórendszerképekre és az alapszintű Docker-parancsokra vonatkozó alapvető ismeretekkel. Amennyiben szükséges, tekintse meg a tárolók alapfogalmainak ismertetését a [Docker használatának első lépései között][docker-get-started].

Az oktatóanyag elvégzéséhez szüksége lesz egy Docker-fejlesztési környezetre. A Docker csomagokat biztosít, amelyekkel a Docker egyszerűen konfigurálható bármely [Mac][docker-for-mac], [Windows][docker-for-windows] vagy [Linux][docker-for-linux] rendszeren.

Az Azure Cloud Shell nem tartalmazza a jelen oktatóanyag lépéseinek elvégzéséhez szükséges Docker-összetevőket, Ezért ajánlott egy teljes Docker fejlesztési környezet használata.

## <a name="get-application-code"></a>Az alkalmazáskód letöltése

A jelen oktatóanyagban használt mintaalkalmazás egy alapszintű szavazóalkalmazás. Az alkalmazás egy előtérbeli webes összetevőből és egy háttérbeli Redis-példányból áll. A webes összetevő egy egyéni tárolórendszerképbe van csomagolva. A Redis-példány a Docker Hubról származó, módosítatlan rendszerképet használ.

A git használatával töltse le az alkalmazás egy másolatát a fejlesztői környezetbe.

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Módosítsa a könyvtárakat, hogy a klónozott könyvtárból dolgozzon.

```console
cd azure-voting-app-redis
```

A könyvtárán belül található meg az alkalmazás forráskódja, egy előre létrehozott Docker Compose-fájl és egy Kubernetes-jegyzékfájl. Ezeket a fájlokat használjuk az oktatóanyagokban.

## <a name="create-container-images"></a>Tárolórendszerképek létrehozása

A [Docker Compose][docker-compose] segítségével automatizálhatja a tárolórendszerképekből való összeállítást és a többtárolós alkalmazások üzembe helyezését.

Futtassa a `docker-compose.yaml` fájlt a tárolórendszerkép létrehozásához, töltse le a Redis-rendszerképet, és indítsa el az alkalmazást.

```console
docker-compose up -d
```

Amikor elkészült, a [docker images][docker-images] paranccsal tekintheti meg a létrehozott rendszerképeket.

```console
docker images
```

Figyelje meg, hogy három rendszerkép lett letöltve vagy jött létre. Az `azure-vote-front` rendszerkép tartalmazza az alkalmazást, és a `nginx-flask` rendszerképet használja alapként. A `redis` rendszerkép használatával indítható el egy Redis-példány.

```
REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Futtassa a [docker ps][docker-ps] parancsot a futó tárolók megtekintéséhez.

```console
docker ps
```

Kimenet:

```
CONTAINER ID        IMAGE             COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        azure-vote-front  "/usr/bin/supervisord"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:8080->80/tcp   azure-vote-front
b68fed4b66b6        redis             "docker-entrypoint..."   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

## <a name="test-application-locally"></a>Az alkalmazás helyi tesztelése

A futó alkalmazás megtekintéséhez nyissa meg a böngészőben a következőt: http://localhost:8080.

![Egy Azure-beli Kubernetes-fürt képe](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Most, hogy az alkalmazás működésének ellenőrzése megtörtént, a futó tárolók leállíthatók és eltávolíthatók. Ne törölje a tárolórendszerképeket. Az `azure-vote-front` rendszerképet a következő oktatóanyagban töltjük fel egy Azure Container Registry-példányba.

Futtassa a következő parancsot a futó tárolók leállításához.

```console
docker-compose stop
```

Törölje a leállított tárolókat és erőforrásokat az alábbi paranccsal.

```console
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
> [Rendszerképek leküldése az Azure Container Registrybe][aks-tutorial-prepare-acr]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-ps]: https://docs.docker.com/engine/reference/commandline/ps/

<!-- LINKS - internal -->
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md