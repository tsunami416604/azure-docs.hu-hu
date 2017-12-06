---
title: "Azure-tárolót beállításjegyzék adattárak"
description: "Azure-tároló beállításjegyzék adattárak Docker lemezképek használata"
services: container-registry
author: cristy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 03/24/2017
ms.author: cristyg
ms.openlocfilehash: 3321dd1d8bbd1b8232c26491edd8c374df16b813
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
---
# <a name="azure-container-registry-repositories"></a>Azure-tárolót beállításjegyzék adattárak

Azure-tárolót beállításjegyzék tároló lemezképek tárolása adattárak teszi lehetővé. Lemezképek tárolása tárházak találhatók, akkor elkülönített környezetben csoportok lemezképet (vagy képeket verzióját). A tárolóhelyekkel történő képek leküldése a beállításjegyzék megadhat.


## <a name="prerequisites"></a>Előfeltételek
* **Azure Container Registry** – Létrehozhat egy tároló-beállításjegyzéket Azure-előfizetésében. Ehhez például használhatja az [Azure Portalt](container-registry-get-started-portal.md) vagy az [Azure CLI 2.0-t](container-registry-get-started-azure-cli.md).
* **A Docker parancssori felülete** – Ha szeretné helyi számítógépét Docker-gazdagépként beállítani és elérni a Docker parancssori felületének parancsait, telepítse a [Docker Engine-t](https://docs.docker.com/engine/installation/).
* **A képfájl lekéréses** - lemezkép nyilvános Docker Hub beállításjegyzékből való lekérésére, címkével, és hogy a beállításjegyzék. Útmutatás a leküldéses és lekéréses képek, lásd: [leküldéses Docker kép Azure személyes beállítási](container-registry-get-started-docker-cli.md).


## <a name="viewing-repositories-in-the-portal"></a>A portálon megtekintik adattárak

A tároló beállításjegyzék képek rendelkezik leküldött, miután az Azure-portálon a lemezképeket tároló adattárak listáját láthatja.

Ha követte a lépéseket a [leküldéses Docker kép Azure személyes beállítási](container-registry-get-started-docker-cli.md) cikk, most rendelkeznie kell egy Nginx-lemezképet a tároló beállításjegyzékben. Utasításokat részeként kell adta meg a lemezkép egy névteret. Az alábbi példában a parancs a "minták" tárházba NGinx kép leküldéses értesítések:

```
docker push myregistry.azurecr.io/samples/nginx
```
 Az Azure Container Registry támogatja a többszintű adattárnévtereket. Ezzel a szolgáltatással egy adott alkalmazáshoz vagy alkalmazások gyűjteményéhez kapcsolódó rendszerképek gyűjteményeit csoportba rendezheti az egyes fejlesztői és üzemeltetői csoportok számára. További lásd: a tároló nyilvántartó adattárak [saját Docker-tároló nyilvántartó az Azure-ban](container-registry-intro.md).

A tároló beállításjegyzék adattárak megtekintése:

1. Jelentkezzen be az Azure portálra.
2. Az a **Azure tároló beállításjegyzék** panelen válassza ki a megvizsgálni kívánt beállításjegyzék
3. A beállításjegyzék paneljén kattintson **Tárházak** a tárolóhelyekkel és a képek listájának megjelenítéséhez
4. (Választható) Válasszon ki egy adott lemezképet címkék megtekintéséhez

![A tárolóhelyekkel a portálon](./media/container-registry-repositories/container-registry-repositories.png)


## <a name="next-steps"></a>Következő lépések
Most, hogy elsajátította az alapokat, készen áll a beállításjegyzéke használatára. Például üzembe helyezhet tárolórendszerképeket egy [Azure Container Service](https://azure.microsoft.com/documentation/services/container-service/)-fürtön.
