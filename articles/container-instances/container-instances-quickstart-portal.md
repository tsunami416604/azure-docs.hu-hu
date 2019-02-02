---
title: Rövid útmutató – alkalmazás futtatása az Azure Container Instances szolgáltatásban – portál
description: Ebben a rövid útmutatóban használhatja az Azure Portalon egy futtathat az Azure Container Instances szolgáltatásban elkülönített tárolót a Docker-tárolóalkalmazás üzembe helyezése
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: quickstart
ms.date: 10/02/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: d6a1d442eca0cf5e433a82fb52ed54b09b56c779
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2019
ms.locfileid: "55566095"
---
# <a name="quickstart-run-a-container-application-in-azure-container-instances-in-the-azure-portal"></a>Gyors útmutató: Egy tároló-alkalmazás futtatása az Azure Container Instances szolgáltatásban az Azure Portalon

Az Azure Container Instances segítségével egyszerűen és gyorsan futtathat Docker-tárolókat az Azure-ban. Nem kell virtuális gépeket üzembe helyeznie vagy teljes körű tárolóvezérlési platformot használnia (amilyen például a Kubernetes). Ebben a rövid útmutatóban az Azure Portal használatával tárolót hozhat létre az Azure-ban, és az alkalmazását egy teljes tartománynévvel (FQDN-nel) elérhetővé teheti. Néhány beállítás konfigurálása és a tároló üzembe helyezése után tallózhat a futó alkalmazásban:

![Az Azure Container Instances szolgáltatásban üzembe helyezett alkalmazás képe a böngészőben][aci-portal-07]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként létrehozhat egy [ingyenes][azure-free-account] fiókot.

## <a name="create-a-container-instance"></a>Tárolópéldány létrehozása

Válassza az **Erőforrás létrehozása** > **Tárolók** > **Container Instances** elemet.

![Új tárolópéldány létrehozásának megkezdése az Azure Portalon][aci-portal-01]

Írja be a következő értékeket a **Tároló neve**, **Tárolórendszerkép**, és **Erőforráscsoport** szövegmezőkbe. A többi értéket hagyja az alapértelmezett értéken, majd válassza az **OK** lehetőséget.

* Tárolónév: `mycontainer`
* Tárolórendszerkép: `microsoft/aci-helloworld`
* Erőforráscsoport: **Új létrehozása** > `myResourceGroup`

![Új tárolópéldány alapbeállításainak konfigurálása az Azure Portalon][aci-portal-03]

Ebben a rövid útmutatóban ne módosítsa az alapértelmezett beállítás a **nyilvános** üzembe helyezéséhez a `microsoft/aci-helloworld` rendszerkép a nyilvános Docker Hub-beállításjegyzékből. Ez a rendszerkép csomagok a node.js-ben, amely egy statikus HTML-oldalt szolgál egy kisméretű webalkalmazást.

A **Konfiguráció** területen adjon meg egy **DNS-névcímkét** a tárolónak. A névnek egyedinek kell lennie abban az Azure-régióban, ahol a tárolópéldányt létrehozza. A tároló nyilvánosan elérhető az alábbi helyen: `<dns-name-label>.<region>.azurecontainer.io`.

Hagyja meg a **Konfiguráció** területen található többi beállítás alapértelmezett értékét, majd válassza az **OK** lehetőséget a konfiguráció érvényesítéséhez.

![Új tárolópéldány konfigurálása az Azure Portalon][aci-portal-04]

Miután az érvényesítés befejeződött, egy összefoglalás jelenik meg a tároló beállításairól. Kattintson az **OK** gombra a tároló üzembe helyezési kérelmének elküldéséhez.

![Új tárolópéldány beállításainak összefoglalása az Azure Portalon][aci-portal-05]

Az üzembe helyezés kezdetekor egy értesítés jelenik meg, amely jelzi, hogy az üzembe helyezés folyamatban van. Amikor a tárolócsoport üzembe helyezése megtörtént, egy újabb értesítés jelenik meg.

![Új tárolópéldány létrehozásának folyamata az Azure Portalon][aci-portal-08]

Nyissa meg a tárolócsoport áttekintését az **Erőforráscsoportok** > **myResourceGroup** > **mycontainer** útvonalon. Jegyezze fel a tárolópéldány teljes tartománynevét (**FQDN**) és **Állapotát**.

![Tárolócsoport áttekintése az Azure Portalon][aci-portal-06]

Ha az **Állapot** *Fut* értékre vált, navigáljon a tároló teljes tartománynevére a böngészőjében.

![Az Azure Container Instances használatával üzembe helyezett alkalmazás képe a böngészőben][aci-portal-07]

Gratulálunk! Csupán néhány beállítás konfigurálásával üzembe helyezett egy nyilvánosan hozzáférhető alkalmazást az Azure Container Instances szolgáltatásban.

## <a name="view-container-logs"></a>Tárolónaplók megtekintése

A tárolópéldányok naplóinak megtekintése hasznos lehet a tárolóval vagy az azon futtatott alkalmazással kapcsolatos hibák elhárítása során.

A tároló naplóinak megtekintéséhez a **Beállítások** területen válassza a **Tárolók**, majd a **Naplók** lehetőséget. Látnia kell a HTTP GET kérést is, amely akkor jött létre, amikor megtekintette az alkalmazást a böngészőjében.

![Tárolónaplók az Azure Portalon][aci-portal-11]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután végzett a tárolóval, válassza ki az **Áttekintés** lehetőséget a *mycontainer* tárolópéldányhoz, majd válassza a **Törlés** lehetőséget.

![Tárolópéldány törlése az Azure Portalon][aci-portal-09]

Válassza az **Igen** lehetőséget, amikor a megerősítési párbeszédpanel megjelenik.

![Tárolópéldány törlésének megerősítése az Azure Portalon][aci-portal-10]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy tárolópéldányt egy a nyilvános Docker Hub beállításjegyzékben található rendszerképből. Ha saját maga szeretne létrehozni és üzembe helyezni egy tárolórendszerképet egy Azure-beli privát tárolóregisztrációs adatbázisból, lépjen tovább az Azure Container Instances oktatóanyagára.

> [!div class="nextstepaction"]
> [Az Azure Container Instances oktatóanyaga](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[aci-portal-01]: ./media/container-instances-quickstart-portal/qs-portal-01.png
[aci-portal-03]: ./media/container-instances-quickstart-portal/qs-portal-03.png
[aci-portal-04]: ./media/container-instances-quickstart-portal/qs-portal-04.png
[aci-portal-05]: ./media/container-instances-quickstart-portal/qs-portal-05.png
[aci-portal-06]: ./media/container-instances-quickstart-portal/qs-portal-06.png
[aci-portal-07]: ./media/container-instances-quickstart-portal/qs-portal-07.png
[aci-portal-08]: ./media/container-instances-quickstart-portal/qs-portal-08.png
[aci-portal-09]: ./media/container-instances-quickstart-portal/qs-portal-09.png
[aci-portal-10]: ./media/container-instances-quickstart-portal/qs-portal-10.png
[aci-portal-11]: ./media/container-instances-quickstart-portal/qs-portal-11.png

<!-- LINKS - External -->
[azure-free-account]: https://azure.microsoft.com/free/