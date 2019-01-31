---
title: Rövid útmutató – privát Docker-tárolójegyzék létrehozása az Azure-ban – Azure portal
description: Gyorsan megismerheti egy privát Docker-tárolójegyzék az Azure Portallal való létrehozásának módját.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 93c22475a4043d1cbf5cb0ad7f9b134e8ac717cc
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55298415"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-portal"></a>Gyors útmutató: Hozzon létre egy privát tárolójegyzékben az Azure portal használatával

Az Azure-beli tároló-beállításjegyzék egy privát Docker-tárolójegyzék az Azure-ban, amelyben tárolhatja és kezelheti privát Docker-tárolóinak rendszerképeit. Ebben a rövid útmutatóban létrehozhat egy tároló-beállításjegyzéket az Azure Portallal. Ezután a Docker-parancsok segítségével továbbít egy rendszerképet a regisztrációs adatbázisba, és végül kérje le, és futtassa a rendszerképet a regisztrációs adatbázisból.

Jelentkezzen be a beállításjegyzék tárolórendszerképek dolgozhat, ehhez a rövid útmutatóhoz, hogy futnak-e az Azure parancssori felület (2.0.55 verzió vagy újabb ajánlott). A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

A Dockert is telepítenie kell helyileg. A Docker csomagokat biztosít, amelyekkel a Docker egyszerűen konfigurálható bármely [Mac][docker-mac], [Windows][docker-windows] vagy [Linux][docker-linux] rendszeren.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása

Válassza az **Erőforrás létrehozása** > **Tárolók** > **Container Registry** elemet.

![Tároló-beállításjegyzék létrehozása az Azure Portalon][qs-portal-01]

Adjon meg értékeket az **Adatbázis neve** és **Erőforráscsoport** mezőkben. A beállításjegyzék nevének egyedinek kell lennie az Azure rendszerben, és 5–50 alfanumerikus karaktert kell tartalmaznia. Ebben a rövid útmutatóban hozzon létre egy `myResourceGroup` nevű új erőforráscsoportot az `West US` nevű helyen, majd a **Termékváltozat** mezőben válassza az „Alapszintű” lehetőséget. Kattintson a **Létrehozás** elemre az ACR-példány üzembe helyezéséhez.

![Tárolóregisztrációs adatbázis létrehozása az Azure Portalon][qs-portal-03]

Ebben a rövid útmutatóban létrehozott egy *alapszintű* beállításjegyzékbe, amely költségek optimalizált megoldás megismerése az Azure Container Registry-fejlesztőknek. További információ az elérhető szolgáltatáscsomagban: [tároló-beállításjegyzék Termékváltozatai][container-registry-skus].

Ha a **üzembe helyezés sikeres** műveletről, válassza ki a tároló-beállításjegyzéket a portálon. 

![Tárolóregisztrációs adatbázis áttekintése az Azure Portalon][qs-portal-05]

Jegyezze fel az értékét a **bejelentkezési kiszolgáló**. Ezt az értéket használja a következő lépések során a beállításjegyzék, az Azure CLI-vel és a Docker használata.

## <a name="log-in-to-registry"></a>Bejelentkezés a beállításjegyzékbe

A tárolórendszerképek mozgatásához először be kell jelentkeznie az ACR-példányba. Nyissa meg a parancs-rendszerhéj az operációs rendszer, és használja a [az acr bejelentkezési] [ az-acr-login] parancsot az Azure CLI-ben.

```azurecli
az acr login --name <acrName>
```

A parancs a `Login Succeeded` üzenetet adja vissza, ha befejeződött. 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Tárolórendszerképek listázása

A tárolójegyzékben található rendszerképek listázásához navigáljon a beállításjegyzékhez a portálon, válassza a **Tárházak**, majd válassza ki a segítségével létrehozott adattárat `docker push`.

Ebben a példában kiválasztjuk a **busybox** tárházat, és láthatja a `v1`-alatt címkézett rendszerképet **CÍMKÉK**.

![Tárolórendszerképek listázása az Azure Portalon][qs-portal-09]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az erőforrások törléséhez keresse meg a **myResourceGroup** erőforráscsoportot a portálon. Az erőforráscsoport betöltése után kattintson a **erőforráscsoport törlése** eltávolítható az erőforráscsoport, a tároló-beállításjegyzék és a tárolórendszerképeket tárolja.

![Erőforráscsoport törlése az Azure Portalon][qs-portal-08]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Azure Container Registry és az Azure portal, leküldtünk bele egy tárolórendszerképet, és a lekért és futtatta a rendszerképet a beállításjegyzékből. Továbbra is az Azure Container Registry oktatóanyagok az ACR alaposabban.

> [!div class="nextstepaction"]
> [Azure Container Registry-oktatóanyagok][container-registry-tutorial-quick-task]

<!-- IMAGES -->
[qs-portal-01]: ./media/container-registry-get-started-portal/qs-portal-01.png
[qs-portal-02]: ./media/container-registry-get-started-portal/qs-portal-02.png
[qs-portal-03]: ./media/container-registry-get-started-portal/qs-portal-03.png
[qs-portal-05]: ./media/container-registry-get-started-portal/qs-portal-05.png
[qs-portal-08]: ./media/container-registry-get-started-portal/qs-portal-08.png
[qs-portal-09]: ./media/container-registry-get-started-portal/qs-portal-09.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-login]: /cli/azure/acr#az-acr-login
