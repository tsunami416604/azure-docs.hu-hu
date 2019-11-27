---
title: Rövid útmutató – Docker-tároló üzembe helyezése a Container instance-portálon
description: Ebben a rövid útmutatóban a Azure Portal használatával gyorsan üzembe helyezhet egy elkülönített Azure Container-példányon futó tároló-webalkalmazást
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: quickstart
ms.date: 04/17/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: e0c5ba57c7664a64c1b11bed215f419f31630d39
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533529"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-portal"></a>Gyors útmutató: tároló-példány üzembe helyezése az Azure-ban a Azure Portal használatával

A Azure Container Instances használatával a kiszolgáló nélküli Docker-tárolókat az Azure-ban, az egyszerűség és a gyorsaság segítségével futtathatja. Igény szerint üzembe helyezhet egy alkalmazást egy tároló-példányon, ha nincs szüksége a teljes Container-előkészítési platformra, például az Azure Kubernetes szolgáltatásra.

Ebben a rövid útmutatóban egy elkülönített Docker-tároló üzembe helyezéséhez, valamint az alkalmazás teljes tartománynévvel (FQDN) való elérhetővé tételéhez használja a Azure Portal. Néhány beállítás konfigurálása és a tároló üzembe helyezése után tallózhat a futó alkalmazásban:

![Az Azure Container Instances szolgáltatásban üzembe helyezett alkalmazás képe a böngészőben][aci-portal-07]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot][azure-free-account] a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-container-instance"></a>Tárolópéldány létrehozása

Válassza az **Erőforrás létrehozása** > **Tárolók** > **Container Instances** elemet.

![Új tárolópéldány létrehozásának megkezdése az Azure Portalon][aci-portal-01]

Az **alapvető beállítások** lapon adja meg a következő értékeket az **erőforráscsoport**, a **tároló neve**és a **tároló képe** szövegmezőben. A többi értéket hagyja az alapértelmezett értéken, majd válassza az **OK** lehetőséget.

* Erőforráscsoport: **Új létrehozása** > `myresourcegroup`
* Tárolónév: `mycontainer`
* Tárolórendszerkép: `mcr.microsoft.com/azuredocs/aci-helloworld`

![Új tárolópéldány alapbeállításainak konfigurálása az Azure Portalon][aci-portal-03]

Ebben a rövid útmutatóban **a nyilvános Microsoft `aci-helloworld`-lemezkép** üzembe helyezéséhez az alapértelmezett **rendszerkép-típust** kell használnia. Ez a linuxos rendszerkép egy, a Node. js-ben írt kisméretű webalkalmazást csomagol, amely egy statikus HTML-oldalt szolgál ki.

A **hálózatkezelés** lapon adja meg a tároló **DNS-neve címkéjét** . A névnek egyedinek kell lennie azon az Azure-régión belül, ahol létrehozza a Container-példányt. A tároló nyilvánosan elérhető az alábbi helyen: `<dns-name-label>.<region>.azurecontainer.io`. Ha „DNS-névcímke nem érhető el” hibaüzenetet kap, próbálkozzon másik DNS-névcímkével.

![Új tárolópéldány konfigurálása az Azure Portalon][aci-portal-04]

Hagyja meg a többi beállítást az alapértelmezett értékeken, majd válassza a **felülvizsgálat + létrehozás**elemet.

Miután az érvényesítés befejeződött, egy összefoglalás jelenik meg a tároló beállításairól. Válassza a **Létrehozás** lehetőséget a tároló üzembe helyezési kérelmének elküldéséhez.

![Új tárolópéldány beállításainak összefoglalása az Azure Portalon][aci-portal-05]

Az üzembe helyezés kezdetekor egy értesítés jelenik meg, amely jelzi, hogy az üzembe helyezés folyamatban van. Amikor a tárolócsoport üzembe helyezése megtörtént, egy újabb értesítés jelenik meg.

Nyissa meg a tároló csoport áttekintését, ehhez navigáljon az **erőforráscsoportok** > **myresourcegroup** > **mycontainer**. Jegyezze fel a tárolópéldány teljes tartománynevét (**FQDN**) és **Állapotát**.

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

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Azure Container-példányt egy nyilvános Microsoft-rendszerképből. Ha saját maga szeretne létrehozni és üzembe helyezni egy tárolórendszerképet egy Azure-beli privát tárolóregisztrációs adatbázisból, lépjen tovább az Azure Container Instances oktatóanyagára.

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