---
title: Gyorsútmutató – Rendszerleíró adatbázis létrehozása a portálon
description: Gyorsan megtudhatja, hogy hozzon létre egy privát Docker-beállításjegyzéket az Azure Container Registry az Azure Portalon.
ms.topic: quickstart
ms.date: 03/03/2020
ms.custom: seodec18, mvc
ms.openlocfilehash: 6fe6358655f50ab783b4017efa8ee1db351cd018
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79409258"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-portal"></a>Rövid útmutató: Hozzon létre egy privát tároló beállításjegyzéket az Azure Portalon

Az Azure-tároló beállításjegyzék egy privát Docker-beállításjegyzék az Azure-ban, ahol tárolhatja és kezelheti a privát Docker-tároló rendszerképek és a kapcsolódó összetevők. Ebben a rövid útmutatóban létrehozhat egy tároló-beállításjegyzéket az Azure Portallal. Ezután a Docker-parancsok használatával leküldéses egy tárolórendszerképet a rendszerleíró adatbázisba, és végül lekéri és futtatja a rendszerképet a rendszerleíró adatbázisból.

A beállításjegyzékbe való bejelentkezéshez a tárolórendszerképek használatához ez a rövid útmutató megköveteli, hogy az Azure CLI (2.0.55-ös vagy újabb verzió ajánlott) futtatásához. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

A Dockert is telepítenie kell helyileg. A Docker csomagokat biztosít, amelyekkel a Docker egyszerűen konfigurálható bármely [Mac][docker-mac], [Windows][docker-windows] vagy [Linux][docker-linux] rendszeren.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása

Válassza **az Erőforrástárolók** > **Containers** > **tárolók beállításjegyzékének**létrehozása lehetőséget.

![Tároló-beállításjegyzék létrehozása az Azure Portalon][qs-portal-01]

Az **Alapok** lapon adja meg az **Erőforráscsoport** és a **Rendszerleíróadatbázis nevének értékeit.** A beállításjegyzék nevének egyedinek kell lennie az Azure rendszerben, és 5–50 alfanumerikus karaktert kell tartalmaznia. Ebben a rövid útmutatóban hozzon létre egy `myResourceGroup` nevű új erőforráscsoportot az `West US` nevű helyen, majd a **Termékváltozat** mezőben válassza az „Alapszintű” lehetőséget. 

![Tárolóbeállításjegyzék létrehozása az Azure Portalon][qs-portal-03]

A fennmaradó beállítások alapértelmezett értékeinek elfogadása. Ezután válassza **a Véleményezés + create lehetőséget.** A beállítások áttekintése után válassza a **Létrehozás gombot.**

Ebben a rövid útmutatóban hozzon létre egy *alapszintű* beállításjegyzéket, amely egy költségoptimalizált lehetőség a fejlesztők számára, hogy megismerjék az Azure Container Registry-t. Az elérhető szolgáltatásszintekről a [Container registry ska-k][container-registry-skus]ban talál további részleteket.

Amikor a **központi telepítés sikeres** üzenet jelenik meg, válassza ki a tároló beállításjegyzéka a portálon. 

![Tárolóbeállítás-beállításjegyzék – áttekintés az Azure Portalon][qs-portal-05]

Vegye figyelembe a **bejelentkezési kiszolgáló**értékét. Ezt az értéket a következő lépésekben használja, amikor leküldéses és lekéréses képek docker.

## <a name="log-in-to-registry"></a>Bejelentkezés a beállításjegyzékbe

A tárolórendszerképek mozgatásához először be kell jelentkeznie az ACR-példányba. Nyisson meg egy parancshéjat az operációs rendszerben, és használja az [az acr bejelentkezési][az-acr-login] parancsot az Azure CLI-ben. (Bejelentkezéskor csak a rendszerleíró adatbázis nevét adja meg. Ne adja meg a "azurecr.io" utótagot.)

```azurecli
az acr login --name <acrName>
```

A parancs a `Login Succeeded` üzenetet adja vissza, ha befejeződött. 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Tárolórendszerképek listázása

A rendszerképek rendszerleíró adatbázisban való listázásához keresse meg a rendszerleíró adatbázist a `docker push`portálon, és válassza az **Adattárak**lehetőséget, majd válassza ki a segítségével létrehozott tárházat.

Ebben a példában kiválasztjuk a **hello-world** adattárat, és láthatjuk a -tagged képet a `v1` **Címkék**alatt.

![Tárolóképek listázása az Azure Portalon][qs-portal-09]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az erőforrások karbantartásához keresse meg a **myResourceGroup erőforráscsoportot** a portálon. Az erőforráscsoport betöltése után kattintson az **Erőforráscsoport törlése** elemre az erőforráscsoport, a tároló beállításjegyzékének és az ott tárolt tárolórendszerképek eltávolításához.

![Erőforráscsoport törlése az Azure Portalon][qs-portal-08]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Azure Container Registry az Azure Portalon, leadott egy tárolórendszerképet, és lekérése és a rendszerkép a beállításjegyzékből. Folytassa az Azure Container Registry oktatóanyagokkal az ACR mélyebb megtekintéséhez.

> [!div class="nextstepaction"]
> [Azure Container Registry oktatóanyagok][container-registry-tutorial-quick-task]

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
