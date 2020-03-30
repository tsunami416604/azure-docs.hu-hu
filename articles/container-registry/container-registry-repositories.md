---
title: Adattárak megtekintése a portálon
description: Az Azure Portal használatával megtekintheti az Azure Container Registry adattárak, amelyek a Docker-tároló rendszerképek és más támogatott összetevők üzemelteti.
ms.topic: article
ms.date: 01/05/2018
ms.openlocfilehash: 1da72706d2554610a685f71199ab14af5e30ce1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456294"
---
# <a name="view-container-registry-repositories-in-the-azure-portal"></a>Tárolóbeállítási adattárak megtekintése az Azure Portalon

Az Azure Container Registry lehetővé teszi a Docker-tárolórendszerképek tárolókban való tárolását. A képek tárolókban való tárolásával a képek (vagy a képek verziói) csoportjait elszigetelt környezetekben tárolhatja. Ezeket az adattárakat megadhatja, amikor leküldéses rendszerképeket a rendszerleíró adatbázisba, és azok tartalmát az Azure Portalon.

## <a name="prerequisites"></a>Előfeltételek

* **Tároló beállításjegyzék:** Hozzon létre egy tároló beállításjegyzéket az Azure-előfizetésben. Használja például az [Azure Portalt](container-registry-get-started-portal.md) vagy az [Azure CLI-t.](container-registry-get-started-azure-cli.md)
* **Docker CLI**: Telepítse a [Docker-t][docker-install] a helyi számítógépre, amely a Docker parancssori felületét biztosítja.
* **Tároló lemezképe:** Egy lemezkép leküldése a tároló rendszerleíró adatbázisába. A képek lenyomására és lehúzására vonatkozó útmutatásért olvassa el [A kép leküldése és lekérése](container-registry-get-started-docker-cli.md).

## <a name="view-repositories-in-azure-portal"></a>Adattárak megtekintése az Azure Portalon

Az Azure Portalon megtekintheti a rendszerképeket üzemeltető tárolók listáját, valamint a rendszerképek címkéit.

Ha követte a [leküldéses és lekérése egy lemezkép](container-registry-get-started-docker-cli.md) (és később nem törölte a lemezképet), rendelkeznie kell egy Nginx-lemezkép a tároló rendszerleíró adatbázisban. A cikkben található utasítások azt határozták meg, hogy a képet `/samples/nginx`névtérrel címkézi fel, a alkalmazásban a "minták" a alkalmazásban. Frissítőként a cikkben megadott [docker leküldéses][docker-push] parancs a következő volt:

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

 Mivel az Azure Container Registry támogatja az ilyen többszintű tárház névterek, hatóköre egy adott alkalmazáshoz kapcsolódó rendszerképek gyűjtemények, vagy egy alkalmazásgyűjtemény, a különböző fejlesztési vagy operatív csapatok. A tároló-jegyzékekben lévő adattárakról a [Privát Docker-tárolók az Azure-ban olvashat bővebben.](container-registry-intro.md)

Tárház megtekintése:

1. Bejelentkezés az [Azure Portalra][portal]
1. Válassza ki azt az **Azure Container Registry-t,** amelyre a Nginx-lemezképet lenyomta
1. Válassza **az Adattárak lehetőséget** a rendszerleíró adatbázisban lévő képeket tartalmazó adattárak listájának megtekintéséhez
1. Válasszon ki egy tárházat az adott tárházban lévő képcímkék megtekintéséhez

Ha például a Nginx-képet a Push és pull in instructed [(Nginx)](container-registry-get-started-docker-cli.md)kép hez rendelt, akkor a következőhöz hasonló tetszőséget kell látnia:

![Adattárak a portálon](./media/container-registry-repositories/container-registry-repositories.png)

## <a name="next-steps"></a>További lépések

Most, hogy már ismeri a portálon lévő adattárak megtekintésének és használatának alapjait, próbálja meg használni az Azure Container Registry egy [Azure Kubernetes-szolgáltatás (AKS)](../aks/tutorial-kubernetes-prepare-app.md) fürtjével.

<!-- LINKS - External -->
[docker-install]: https://docs.docker.com/engine/installation/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[portal]: https://portal.azure.com
