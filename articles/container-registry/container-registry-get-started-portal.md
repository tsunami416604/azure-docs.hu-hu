---
title: Rövid útmutató – beállításjegyzék létrehozása a portálon – Azure Container Registry
description: Gyorsan megtudhatja, hogyan hozhat létre egy privát Docker-beállításjegyzéket a Azure Container Registry a Azure Portal.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: bcf0874266aa83a4b4b932670557854c4688d400
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931635"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-portal"></a>Rövid útmutató: saját tároló-beállításjegyzék létrehozása a Azure Portal használatával

Az Azure-beli tároló-beállításjegyzék egy privát Docker-tárolójegyzék az Azure-ban, amelyben tárolhatja és kezelheti privát Docker-tárolóinak rendszerképeit. Ebben a rövid útmutatóban létrehozhat egy tároló-beállításjegyzéket az Azure Portallal. Ezután a Docker-parancsokkal leküldheti a tárolók rendszerképét a beállításjegyzékbe, és végül lekérdezheti és futtathatja a rendszerképet a beállításjegyzékből.

Ahhoz, hogy bejelentkezzen a beállításjegyzékbe a tároló-lemezképek használatához, ehhez a rövid útmutatóhoz az Azure CLI-t (2.0.55 vagy újabb verzió ajánlott) kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

A Dockert is telepítenie kell helyileg. A Docker csomagokat biztosít, amelyekkel a Docker egyszerűen konfigurálható bármely [Mac][docker-mac], [Windows][docker-windows] vagy [Linux][docker-linux] rendszeren.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása

Válassza az **Erőforrás létrehozása** > **Tárolók** > **Container Registry** elemet.

![Tároló-beállításjegyzék létrehozása az Azure Portalon][qs-portal-01]

Adjon meg értékeket az **Adatbázis neve** és **Erőforráscsoport** mezőkben. A beállításjegyzék nevének egyedinek kell lennie az Azure rendszerben, és 5–50 alfanumerikus karaktert kell tartalmaznia. Ebben a rövid útmutatóban hozzon létre egy `West US` nevű új erőforráscsoportot az `myResourceGroup` nevű helyen, majd a **Termékváltozat** mezőben válassza az „Alapszintű” lehetőséget. Kattintson a **Létrehozás** elemre az ACR-példány üzembe helyezéséhez.

![Tároló-beállításjegyzék létrehozása a Azure Portal][qs-portal-03]

Ebben a rövid útmutatóban egy *alapszintű* beállításjegyzéket hozunk létre, amely egy költséghatékony megoldás a fejlesztők számára a Azure Container Registry megismeréséhez. A rendelkezésre álló szolgáltatási szintek részletes ismertetését lásd: [Container Registry SKU][container-registry-skus]-i.

Amikor megjelenik az **üzembe helyezés sikeres** üzenet, válassza ki a tároló-beállításjegyzéket a portálon. 

![A Container Registry áttekintése a Azure Portal][qs-portal-05]

Jegyezze fel a **bejelentkezési kiszolgáló**értékét. Ezt az értéket a következő lépésekben használhatja, miközben az Azure CLI és a Docker használatával dolgozik a beállításjegyzékben.

## <a name="log-in-to-registry"></a>Bejelentkezés a beállításjegyzékbe

A tárolórendszerképek mozgatásához először be kell jelentkeznie az ACR-példányba. Nyisson meg egy parancssori felületet az operációs rendszeren, és használja az az [ACR login][az-acr-login] parancsot az Azure CLI-ben.

```azurecli
az acr login --name <acrName>
```

A parancs a `Login Succeeded` üzenetet adja vissza, ha befejeződött. 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Tárolórendszerképek listázása

A beállításjegyzékben található rendszerképek listázásához navigáljon a beállításjegyzékhez a portálon, válassza a **Tárházak**lehetőséget, majd válassza ki a `docker push`használatával létrehozott tárházat.

Ebben a példában a **Hello-World** tárházat választjuk, és a **címkék**területen láthatjuk a `v1`címkézett képet.

![Tároló lemezképek listázása a Azure Portal][qs-portal-09]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az erőforrások tisztításához navigáljon az **myResourceGroup** erőforráscsoporthoz a portálon. Az erőforráscsoport betöltését követően kattintson az **erőforráscsoport törlése** elemre az erőforráscsoport, a tároló-beállításjegyzék és az ott tárolt tároló-lemezképek eltávolításához.

![Erőforráscsoport törlése az Azure Portalon][qs-portal-08]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Azure Container Registryt a Azure Portal, leküldte a tároló képét, és lehúzta és futtatta a rendszerképet a beállításjegyzékből. Folytassa a Azure Container Registry oktatóanyagokkal, és tekintse meg az ACR mélyebb megjelenését.

> [!div class="nextstepaction"]
> [Oktatóanyagok Azure Container Registry][container-registry-tutorial-quick-task]

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
