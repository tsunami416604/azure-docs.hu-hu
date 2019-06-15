---
title: Az Azure Portalon az Azure Container Registry-adattárak
description: Tudnivalók az Azure Container Registry-tárházak megtekintése az Azure Portalon.
services: container-registry
author: cristy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 01/05/2018
ms.author: cristyg
ms.openlocfilehash: 685c978ff206e75d770918f2528a826ad522b706
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64710183"
---
# <a name="view-container-registry-repositories-in-the-azure-portal"></a>Container registry-tárházak megtekintése az Azure Portalon

Az Azure Container Registry tárolását teszi Docker-tárházak esetében tárolórendszerképek. Tárolja a lemezképeket tárházakban, képek (vagy képek verziói) csoportok izolált környezetekben tárolhatja. Az alábbi két adattár is megadhat, amikor rendszerképek leküldése a tárolójegyzékbe, és azok tartalmának megtekintése az Azure Portalon.

## <a name="prerequisites"></a>Előfeltételek

* **Tárolóregisztrációs adatbázis**: Tároló-beállításjegyzék létrehozása az Azure-előfizetésében. Például a [az Azure portal](container-registry-get-started-portal.md) vagy a [Azure CLI-vel](container-registry-get-started-azure-cli.md).
* **Docker CLI**: Telepítés [Docker] [ docker-install] a helyi gépen, amely biztosít a Docker parancssori felületén.
* **Tárolórendszerkép**: Rendszerkép leküldése a tárolójegyzékbe. Leküldéses és lekéréses képek való útmutatásért lásd: [lekéréses és küldéses kép](container-registry-get-started-docker-cli.md).

## <a name="view-repositories-in-azure-portal"></a>Az adattárak megtekintése az Azure Portalon

Üzemeltetés, a képek, valamint a címkéket, az Azure Portalon a tárházak listáját láthatja.

Ha követte a lépéseket a [lekéréses és küldéses kép](container-registry-get-started-docker-cli.md) (és később nem törli a lemezkép), meg kell rendelkeznie egy Nginx rendszerképet a tárolóregisztrációs adatbázisba. A cikkben lévő utasítások meg, hogy a címke a kép és a egy névtér, a "minták" `/samples/nginx`. Frissítő, mint a [docker leküldéses] [ docker-push] volt ebben a cikkben megadott parancsot:

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

 Mivel az Azure Container Registry támogatja az ilyen többszintű adattárnévtereket, egy adott alkalmazás vagy más fejlesztői és üzemeltetői csoportok számára, alkalmazások gyűjteményéhez kapcsolódó rendszerképek gyűjteményeit gazdagépcsoportjaira. További információ a tároló-beállításjegyzékek adattárait, lásd: [privát Docker-tárolójegyzékek az Azure-ban](container-registry-intro.md).

Egy adattár megtekintése:

1. Jelentkezzen be a [Azure Portalon][portal]
1. Válassza ki a **Azure Container Registry** , amelyhez az Nginx rendszerképet leküldte
1. Válassza ki **Tárházak** megjeleníthetők, amelyek a képeket a beállításjegyzék-adattárak
1. Egy adattár belül az adott tárházba rendszerkép címkéinek megtekintéséhez válassza ki

Például, ha az Nginx rendszerképet, leküldött arra utasította a [lekéréses és küldéses kép](container-registry-get-started-docker-cli.md), hasonló kell megjelennie:

![Tárházak a portálon](./media/container-registry-repositories/container-registry-repositories.png)

## <a name="next-steps"></a>További lépések

Most, hogy elsajátította az alapokat, megtekintése és használata a portálon adattárait, próbálja ki az Azure Container Registry használatával egy [Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md) fürt.

<!-- LINKS - External -->
[docker-install]: https://docs.docker.com/engine/installation/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[portal]: https://portal.azure.com
