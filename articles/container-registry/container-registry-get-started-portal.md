---
title: Rövid útmutató – beállításjegyzék létrehozása a portálon
description: Gyorsan megtudhatja, hogyan hozhat létre egy privát Docker-beállításjegyzéket a Azure Container Registry a Azure Portal.
ms.topic: quickstart
ms.date: 03/03/2020
ms.custom: seodec18, mvc
ms.openlocfilehash: 0875e5be628ddfe47696a9d4fc537a8a07122804
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682802"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-portal"></a>Rövid útmutató: saját tároló-beállításjegyzék létrehozása a Azure Portal használatával

Az Azure Container Registry egy privát Docker-beállításjegyzék az Azure-ban, ahol tárolhatja és kezelheti a privát Docker-tárolók lemezképeit és a kapcsolódó összetevőket. Ebben a rövid útmutatóban létrehozhat egy tároló-beállításjegyzéket az Azure Portallal. Ezután a Docker-parancsokkal leküldheti a tárolók rendszerképét a beállításjegyzékbe, és végül lekérdezheti és futtathatja a rendszerképet a beállításjegyzékből.

Ahhoz, hogy bejelentkezzen a beállításjegyzékbe a tároló-lemezképek használatához, ehhez a rövid útmutatóhoz az Azure CLI-t (2.0.55 vagy újabb verzió ajánlott) kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

A Dockert is telepítenie kell helyileg. A Docker csomagokat biztosít, amelyekkel a Docker egyszerűen konfigurálható bármely [Mac][docker-mac], [Windows][docker-windows] vagy [Linux][docker-linux] rendszeren.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása

Válassza **az erőforrás létrehozása**  >  **tárolók**  >  **Container Registry**elemet.

![Tároló-beállításjegyzék létrehozása az Azure Portalon][qs-portal-01]

Az **alapvető beállítások** lapon adja meg az **erőforráscsoport** és a **beállításjegyzék nevének**értékét. A beállításjegyzék nevének egyedinek kell lennie az Azure rendszerben, és 5–50 alfanumerikus karaktert kell tartalmaznia. Ebben a rövid útmutatóban hozzon létre egy `myResourceGroup` nevű új erőforráscsoportot az `West US` nevű helyen, majd a **Termékváltozat** mezőben válassza az „Alapszintű” lehetőséget. 

![Tároló-beállításjegyzék létrehozása a Azure Portal][qs-portal-03]

Fogadja el az alapértelmezett értékeket a többi beállításhoz. Ezután válassza a **felülvizsgálat + létrehozás**elemet. A beállítások áttekintése után válassza a **Létrehozás**lehetőséget.

Ebben a rövid útmutatóban egy *alapszintű* beállításjegyzéket hozunk létre, amely egy költséghatékony megoldás a fejlesztők számára a Azure Container Registry megismeréséhez. A rendelkezésre álló szolgáltatási szintek részletes ismertetését lásd: a [Container Registry szolgáltatási szintjei][container-registry-skus].

Amikor megjelenik az **üzembe helyezés sikeres** üzenet, válassza ki a tároló-beállításjegyzéket a portálon. 

![A Container Registry áttekintése a Azure Portal][qs-portal-05]

Jegyezze fel a **bejelentkezési kiszolgáló**értékét. Ezt az értéket a következő lépésekben hajtja végre a képek Docker használatával történő leküldésekor és lekérése során.

## <a name="log-in-to-registry"></a>Bejelentkezés a beállításjegyzékbe

A tárolórendszerképek mozgatásához először be kell jelentkeznie az ACR-példányba. Nyisson meg egy parancssori felületet az operációs rendszeren, és használja az az [ACR login][az-acr-login] parancsot az Azure CLI-ben. (Csak a beállításjegyzék nevét adja meg a bejelentkezéskor. Ne adja meg a "azurecr.io" utótagot.)

```azurecli
az acr login --name <acrName>
```

A parancs a `Login Succeeded` üzenetet adja vissza, ha befejeződött. 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Tárolórendszerképek listázása

A beállításjegyzékben található rendszerképek listázásához navigáljon a beállításjegyzékhez a portálon, és válassza a **Tárházak**lehetőséget, majd válassza ki a létrehozott tárházat `docker push` .

Ebben a példában a **Hello-World** tárházat választjuk, és a címkék alatt láthatjuk a `v1` -Tagged **Tags**képet.

![Tároló lemezképek listázása a Azure Portal][qs-portal-09]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

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
