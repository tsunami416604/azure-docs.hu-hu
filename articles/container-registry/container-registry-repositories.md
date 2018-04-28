---
title: Az Azure tároló beállításjegyzék tárházak találhatók, az Azure-portálon
description: How Azure tároló beállításjegyzék adattárak megtekintéséhez az Azure portálon.
services: container-registry
author: cristy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 01/05/2018
ms.author: cristyg
ms.openlocfilehash: 4479c78eb2eb4138dbacfa3619616a3b16d51121
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="view-container-registry-repositories-in-the-azure-portal"></a>Tároló beállításjegyzék adattárak tekintse meg az Azure-portálon

Az Azure tároló beállításjegyzék tárolását teszi Docker adattárak lemezképeit tároló. Lemezképek tárolása tárházak találhatók, elkülönített környezetben tárolhatja a képet (vagy a lemezképek verzióit) csoportját. Megadhatja a tárolóhelyekkel képek leküldése a beállításjegyzékben, és a tartalom megtekintéséhez az Azure portálon.

## <a name="prerequisites"></a>Előfeltételek

* **Tároló beállításjegyzék**: hozzon létre egy tároló beállításjegyzék az Azure-előfizetéshez. Tegyük fel például, a [Azure-portálon](container-registry-get-started-portal.md) vagy a [Azure CLI](container-registry-get-started-azure-cli.md).
* **A docker parancssori felület**: telepítése [Docker] [ docker-install] a helyi számítógépen, amely lehetővé teszi a Docker parancssori felületet.
* **Tároló kép**: lemezkép leküldése a tároló beállításjegyzék. Leküldéses és lekéréses képek kapcsolatos útmutatásért lásd: [leküldéses és lekéréses lemezkép](container-registry-get-started-docker-cli.md).

## <a name="view-repositories-in-azure-portal"></a>Nézet adattárak Azure-portálon

A képek, valamint a lemezkép címkéket, az Azure portálon üzemeltető adattárak listáját láthatja.

Ha követte a lépéseket [leküldéses és lekéréses lemezkép](container-registry-get-started-docker-cli.md) (és később nem törli a kép), a tároló beállításjegyzékben kell Nginx-lemezkép. A cikkben lévő utasítások meg, hogy a címke a kép névtérrel, a "minták" `/samples/nginx`. A frissítő, mint a [docker leküldéses] [ docker-push] cikkben megadva parancs a következő volt:

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

 Azure-tároló beállításjegyzék támogatja az ilyen többszintű tárház névterek, mert hatókörét megadhatja egy adott alkalmazást, vagy az alkalmazások különböző fejlesztési vagy működési gyűjtemény kapcsolódó képek gyűjteményei. További lásd: a tároló nyilvántartó adattárak [saját Docker-tároló nyilvántartó az Azure-ban](container-registry-intro.md).

A tárház megtekintése:

1. Jelentkezzen be a [Azure-portálon][portal]
1. Válassza ki a **Azure tároló beállításjegyzék** , amelyhez a Nginx kép leküldött
1. Válassza ki **Tárházak** a beállításjegyzékben a képet tartalmazó adattárak listájának megtekintéséhez
1. Válassza ki a tárház tekintse meg a lemezkép címkék adott tárház belül

Például, ha Ön leküldött Nginx képként arra utasította a [leküldéses és lekéréses lemezkép](container-registry-get-started-docker-cli.md), megtekintheti az alábbihoz hasonlót:

![A tárolóhelyekkel a portálon](./media/container-registry-repositories/container-registry-repositories.png)

## <a name="next-steps"></a>További lépések

Most, hogy tudja, megtekintése és használata a portálon adattárak alapjait, próbálja meg az Azure-tároló beállításjegyzék használatával egy [Azure tároló szolgáltatás (AKS)](../aks/tutorial-kubernetes-prepare-app.md) fürt.

<!-- LINKS - External -->
[docker-install]: https://docs.docker.com/engine/installation/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[portal]: https://portal.azure.com
