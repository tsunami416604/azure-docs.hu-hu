---
title: Rövid útmutató – beállításjegyzék létrehozása a portálon
description: Gyorsan megtudhatja, hogyan hozhat létre egy privát Docker-beállításjegyzéket a Azure Container Registry a Azure Portal.
ms.topic: quickstart
ms.date: 06/11/2020
ms.custom: seodec18, mvc
ms.openlocfilehash: 82f9a6b02832b718d5b4e7b662c590f1992af595
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/12/2020
ms.locfileid: "84752868"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-portal"></a>Rövid útmutató: saját tároló-beállításjegyzék létrehozása a Azure Portal használatával

Az Azure Container Registry egy privát Docker-beállításjegyzék az Azure-ban, ahol tárolhatja és kezelheti a privát Docker-tárolók lemezképeit és a kapcsolódó összetevőket. Ebben a rövid útmutatóban létrehozhat egy tároló-beállításjegyzéket az Azure Portallal. Ezután a Docker-parancsokkal leküldheti a tárolók rendszerképét a beállításjegyzékbe, és végül lekérdezheti és futtathatja a rendszerképet a beállításjegyzékből.

Ahhoz, hogy bejelentkezzen a beállításjegyzékbe a tároló-lemezképek használatához, ehhez a rövid útmutatóhoz az Azure CLI-t (2.0.55 vagy újabb verzió ajánlott) kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

A Dockert is telepítenie kell helyileg. A Docker csomagokat biztosít, amelyekkel a Docker egyszerűen konfigurálható bármely [Mac][docker-mac], [Windows][docker-windows] vagy [Linux][docker-linux] rendszeren.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása

Válassza **az erőforrás létrehozása**  >  **tárolók**  >  **Container Registry**elemet.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-01.png" alt-text="A tároló beállításjegyzékének navigálása a portálon":::

Az **alapvető beállítások** lapon adja meg az **erőforráscsoport** és a **beállításjegyzék nevének**értékét. A beállításjegyzék nevének egyedinek kell lennie az Azure rendszerben, és 5–50 alfanumerikus karaktert kell tartalmaznia. Ebben a rövid útmutatóban hozzon létre egy `myResourceGroup` nevű új erőforráscsoportot az `West US` nevű helyen, majd a **Termékváltozat** mezőben válassza az „Alapszintű” lehetőséget.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-03.png" alt-text="Tároló-beállításjegyzék létrehozása a portálon":::

Fogadja el az alapértelmezett értékeket a többi beállításhoz. Ezután válassza a **felülvizsgálat + létrehozás**elemet. A beállítások áttekintése után válassza a **Létrehozás**lehetőséget.

Ebben a rövid útmutatóban egy *alapszintű* beállításjegyzéket hozunk létre, amely egy költséghatékony megoldás a fejlesztők számára a Azure Container Registry megismeréséhez. Az elérhető szolgáltatási szintek (SKU-EK) részletes ismertetését lásd: a [Container Registry szolgáltatási szintjei][container-registry-skus].

Amikor megjelenik az **üzembe helyezés sikeres** üzenet, válassza ki a tároló-beállításjegyzéket a portálon. 

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-05.png" alt-text="A tároló beállításjegyzékének áttekintése a portálon":::

Jegyezze fel a **bejelentkezési kiszolgáló**értékét. Ezt az értéket a következő lépésekben hajtja végre a képek Docker használatával történő leküldésekor és lekérése során.

## <a name="log-in-to-registry"></a>Bejelentkezés a beállításjegyzékbe

A tároló-lemezképek leküldése és húzása előtt be kell jelentkeznie a beállításjegyzék-példányba. [Jelentkezzen be az Azure CLI][get-started-with-azure-cli] -be a helyi gépen, majd futtassa az az [ACR login][az-acr-login] parancsot. (Csak a beállításjegyzék nevét adja meg az Azure CLI-vel való bejelentkezéskor. Ne adja meg a "azurecr.io" utótagot.)

```azurecli
az acr login --name <registry-name>
```

A parancs a `Login Succeeded` üzenetet adja vissza, ha befejeződött. 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Tárolórendszerképek listázása

A beállításjegyzékben található rendszerképek listázásához navigáljon a beállításjegyzékhez a portálon, és válassza a **tárak**lehetőséget, majd válassza ki a létrehozott **Hello-World** tárházat `docker push` .

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-09.png" alt-text="Tároló lemezképek listázása a portálon":::

A **Hello-World** adattár kiválasztásával megtekintheti a címkével ellátott `v1` képet a **címkék**területen.

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Az erőforrások tisztításához navigáljon az **myResourceGroup** erőforráscsoporthoz a portálon. Miután betöltötte az erőforráscsoportot, kattintson az **erőforráscsoport törlése** elemre az erőforráscsoport, a tároló-beállításjegyzék és az ott tárolt tároló-lemezképek eltávolításához.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-08.png" alt-text="Erőforráscsoport törlése a portálon":::


## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Azure Container Registryt a Azure Portal, leküldte a tároló képét, és lehúzta és futtatta a rendszerképet a beállításjegyzékből. Folytassa a Azure Container Registry oktatóanyagokkal, és tekintse meg az ACR mélyebb megjelenését.

> [!div class="nextstepaction"]
> [Oktatóanyagok Azure Container Registry][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Azure Container Registry feladatok – oktatóanyagok][container-registry-tutorial-quick-task]

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
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
[container-registry-skus]: container-registry-skus.md
[azure-cli]: /cli/azure/install-azure-cli
[get-started-with-azure-cli]: /cli/azure/get-started-with-azure-cli
[az-acr-login]: /cli/azure/acr#az-acr-login
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
