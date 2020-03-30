---
title: fájl belefoglalása
description: fájl belefoglalása
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 01/23/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 09eaf9465ec3912dea6e1f3ee1693f6bfed50abc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "67179675"
---
## <a name="push-image-to-registry"></a>Rendszerkép leküldése a beállításjegyzékbe

Ahhoz, hogy rendszerképet tudjon küldeni egy Azure Container Registry tárolóregisztrációs adatbázisba, először szüksége van egy rendszerképre. Ha még nem rendelkezik helyi tárolórendszerképekkel, futtassa a következő [docker-lekéréses][docker-pull] parancsot egy meglévő rendszerkép lekérése a Docker Hubból. Ebben a példában `hello-world` húzza ki a képet.

```
docker pull hello-world
```

Mielőtt leküldhetné a rendszerképet a regisztrációs adatbázisba, fel kell címkéznie az ACR bejelentkezési kiszolgálójának teljes nevével. A bejelentkezési kiszolgáló neve a * \<rendszerleíró\>adatbázis neve .azurecr.io* formátumban (az összes kisbetű), például *mycontainerregistry007.azurecr.io*.

Címkézze fel a rendszerképet a [docker tag][docker-tag] parancs használatával. Helyettesítse be az `<acrLoginServer>` helyére az ACR-példány bejelentkezési kiszolgálójának nevét.

```
docker tag hello-world <acrLoginServer>/hello-world:v1
```

Végül a [docker push][docker-push] paranccsal küldje le a rendszerképet az ACR-példányba. Helyettesítse be az `<acrLoginServer>` helyére az ACR-példány bejelentkezési kiszolgálójának nevét. Ebben a példában létrehozza a **hello-world** tárház, amely a `hello-world:v1` rendszerképet.

```
docker push <acrLoginServer>/hello-world:v1
```

Miután lelökte a rendszerképet `hello-world:v1` a tároló beállításjegyzékébe, távolítsa el a rendszerképet a helyi Docker-környezetből. (Vegye figyelembe, hogy ez a [docker rmi][docker-rmi] parancs nem távolítja el a rendszerképet a hello-world tárházból az **Azure-tároló** beállításjegyzékében.)

```
docker rmi <acrLoginServer>/hello-world:v1
```

<!-- LINKS - External -->
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/

<!-- LINKS - Internal -->

