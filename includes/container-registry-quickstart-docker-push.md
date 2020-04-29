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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "67179675"
---
## <a name="push-image-to-registry"></a>Rendszerkép leküldése a beállításjegyzékbe

Ahhoz, hogy rendszerképet tudjon küldeni egy Azure Container Registry tárolóregisztrációs adatbázisba, először szüksége van egy rendszerképre. Ha még nem rendelkezik helyi tároló lemezképekkel, futtassa a következő [Docker pull][docker-pull] parancsot egy meglévő rendszerkép lekéréséhez a Docker hub-ból. Ebben a példában a `hello-world` rendszerkép lekérése.

```
docker pull hello-world
```

Mielőtt leküldhetné a rendszerképet a regisztrációs adatbázisba, fel kell címkéznie az ACR bejelentkezési kiszolgálójának teljes nevével. A bejelentkezési kiszolgáló nevét a következő formátumban *mycontainerregistry007.azurecr.io* * \<kell megadni: Registry\>-Name. azurecr.IO* (mind kisbetűs), például mycontainerregistry007.azurecr.IO.

Címkézze fel a rendszerképet a [docker tag][docker-tag] parancs használatával. Helyettesítse be az `<acrLoginServer>` helyére az ACR-példány bejelentkezési kiszolgálójának nevét.

```
docker tag hello-world <acrLoginServer>/hello-world:v1
```

Végül a [docker push][docker-push] paranccsal küldje le a rendszerképet az ACR-példányba. Helyettesítse be az `<acrLoginServer>` helyére az ACR-példány bejelentkezési kiszolgálójának nevét. Ez a példa a **Hello-World** tárházat hozza létre `hello-world:v1` , amely tartalmazza a képet.

```
docker push <acrLoginServer>/hello-world:v1
```

Miután leküldte a rendszerképet a tároló-beállításjegyzékbe, távolítsa el a `hello-world:v1` rendszerképet a helyi Docker-környezetből. (Vegye figyelembe, hogy ez a [Docker RMI][docker-rmi] -parancs nem távolítja el a rendszerképet a **Hello-World** adattárból az Azure Container registryben.)

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

