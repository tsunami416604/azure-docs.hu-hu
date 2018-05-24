---
title: Rövid útmutató – Az első Azure Container Instances-tároló létrehozása az Azure Portal segítségével
description: Ebben a rövid útmutatóban az Azure Portal használatával helyez üzembe egy tárolót az Azure Container Instances szolgáltatásban
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: quickstart
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 6aa6fb27b2aa7c8b9614e5812fadc629b1e185f8
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2018
---
# <a name="quickstart-create-your-first-container-in-azure-container-instances"></a>Rövid útmutató: Az első tároló létrehozása az Azure Container Instances szolgáltatásban

Az Azure Container Instances segítségével egyszerűen hozhat létre és felügyelhet Docker-tárolókat az Azure-ban anélkül, hogy virtuális gépeket kellene kiépítenie, vagy egy magasabb szolgáltatási szintre kellene váltania. Ebben a rövid útmutatóban az Azure Portal használatával létrehozhat tárolót az Azure-ban, és közzéteheti az interneten egy teljes tartománynévvel (FQDN-nel). Néhány beállítás konfigurálása után a következőt láthatja a böngészőben:

![Az Azure Container Instances használatával üzembe helyezett alkalmazás képe a böngészőben][aci-portal-07]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként létrehozhat egy [ingyenes][azure-free-account] fiókot.

## <a name="create-a-container-instance"></a>Tárolópéldány létrehozása

Válassza az **Erőforrás létrehozása** > **Tárolók** > **Container Instances** elemet.

![Új tárolópéldány létrehozásának megkezdése az Azure Portalon][aci-portal-01]

Írja be a következő értékeket a **Tároló neve**, **Tárolórendszerkép**, és **Erőforráscsoport** szövegmezőkbe. A többi értéket hagyja az alapértelmezett értéken, majd válassza az **OK** lehetőséget.

* Tárolónév: `mycontainer`
* Tárolórendszerkép: `microsoft/aci-helloworld`
* Erőforráscsoport: `myResourceGroup`

![Új tárolópéldány alapbeállításainak konfigurálása az Azure Portalon][aci-portal-03]

Windows- és Linux-tárolókat is létrehozhat az Azure Container Instances használatával. Ebben a rövid útmutatóban hagyja meg a **Linux** alapértelmezett beállítást a Linux-alapú `microsoft/aci-helloworld` rendszerkép üzembe helyezéséhez.

A **Konfiguráció** területen adjon meg egy **DNS-névcímkét** a tárolónak. A névnek egyedinek kell lennie abban az Azure-régióban, ahol a tárolópéldányt létrehozza. A tároló nyilvánosan elérhető az alábbi helyen: `<dns-name-label>.<region>.azurecontainer.io`.

Hagyja meg a **Konfiguráció** területen található többi beállítás alapértelmezett értékét, majd válassza az **OK** lehetőséget a konfiguráció érvényesítéséhez.

![Új tárolópéldány konfigurálása az Azure Portalon][aci-portal-04]

Miután az érvényesítés befejeződött, egy összefoglalás jelenik meg a tároló beállításairól. Kattintson az **OK** gombra a tároló üzembe helyezési kérelmének elküldéséhez.

![Új tárolópéldány beállításainak összefoglalása az Azure Portalon][aci-portal-05]

Az üzembe helyezés kezdetekor az irányítópulton egy csempe jelenik meg, amely jelzi, hogy az üzembe helyezés folyamatban van. Az üzembe helyezés végeztével a csempe az új tárolópéldányt jeleníti meg.

![Új tárolópéldány létrehozásának folyamata az Azure Portalon][aci-portal-08]

Válassza ki a **mycontainer** tárolópéldányt a tárolópéldány tulajdonságainak megjelenítéséhez. Jegyezze fel a tárolópéldány teljes tartománynevét (**FQDN**) és **Állapotát**.

![Tárolócsoport áttekintése az Azure Portalon][aci-portal-06]

Ha az **Állapot** *Fut* értékre vált, navigáljon a tároló teljes tartománynevére a böngészőjében.

![Az Azure Container Instances használatával üzembe helyezett alkalmazás képe a böngészőben][aci-portal-07]

Gratulálunk! Csupán néhány beállítás konfigurálásával üzembe helyezett egy nyilvánosan hozzáférhető alkalmazást az Azure Container Instances szolgáltatásban.

## <a name="view-container-logs"></a>Tárolónaplók megtekintése

A tárolópéldányok naplóinak megtekintése hasznos lehet a tárolóval vagy az azon futtatott alkalmazással kapcsolatos hibák elhárítása során.

A tároló naplóinak megtekintéséhez válassza a **BEÁLLÍTÁSOK** területen a **Tárolók**, majd a **Naplók** lehetőséget. Látnia kell a HTTP GET kérést is, amely akkor jött létre, amikor megtekintette az alkalmazást a böngészőjében.

![Tárolónaplók az Azure Portalon][aci-portal-11]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután végzett a tárolóval, válassza ki az **Áttekintés** lehetőséget a *mycontainer* tárolópéldányhoz, majd válassza a **Törlés** lehetőséget.

![Tárolópéldány törlése az Azure Portalon][aci-portal-09]

Válassza az **Igen** lehetőséget, amikor a megerősítési párbeszédpanel megjelenik.

![Tárolópéldány törlésének megerősítése az Azure Portalon][aci-portal-10]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy tárolópéldányt egy a nyilvános Docker Hub beállításjegyzékben található rendszerképből. Ha saját maga szeretne létrehozni és üzembe helyezni egy tárolórendszerképet az Azure Container Instances szolgáltatásban egy privát Azure-tárolóregisztrációs adatbázisból, lépjen tovább az Azure Container Instances oktatóanyagára.

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