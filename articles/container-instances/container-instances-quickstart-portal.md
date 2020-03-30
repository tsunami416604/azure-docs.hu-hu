---
title: Rövid útmutató – Docker-tároló üzembe helyezése a tárolópéldányhoz – Portal
description: Ebben a rövid útmutatóban az Azure Portal segítségével gyorsan üzembe helyezhet egy tárolóba helyezett webalkalmazást, amely egy elszigetelt Azure-tárolópéldányban fut
ms.topic: quickstart
ms.date: 03/09/2020
ms.custom: seodec18, mvc
ms.openlocfilehash: 7a872e955db46b76d3b12f8ffc38d4a8e497ea63
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79087986"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-portal"></a>Rövid útmutató: Tárolópéldány üzembe helyezése az Azure-ban az Azure Portalon

Azure Container Instances használatával kiszolgáló nélküli Docker-tárolók at Azure-ban az egyszerűség és a sebesség használatával. Egy alkalmazást igény szerint egy tárolópéldányra telepíthet, ha nincs szüksége egy teljes tárolóvezelési platformra, például az Azure Kubernetes-szolgáltatásra.

Ebben a rövid útmutatóban az Azure Portalhasználatával üzembe helyezhet egy elkülönített Docker-tárolót, és elérhetővé teheti az alkalmazást egy teljesen minősített tartománynévvel (FQDN). Néhány beállítás konfigurálása és a tároló üzembe helyezése után tallózhat a futó alkalmazásban:

![Az Azure Container Instances szolgáltatáshoz üzembe helyezett alkalmazás képe a böngészőben][aci-portal-07]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,][azure-free-account] mielőtt elkezdené.

## <a name="create-a-container-instance"></a>Tárolópéldány létrehozása

Válassza az > **Erőforrás-tárolótároló-példányok** >  **létrehozása****lehetőséget.**

![Új tárolópéldány létrehozásának megkezdése az Azure Portalon][aci-portal-01]

Az **Alapok lapon** adja meg a következő értékeket az **Erőforrás csoport**, a **Tároló neve**és a **Tároló képszövegmezőbe.** A többi értéket hagyja az alapértelmezett értéken, majd válassza az **OK** lehetőséget.

* Erőforráscsoport: **Új** > `myresourcegroup`
* Tárolónév: `mycontainer`
* Kép forrása: **Rövid útmutató képek**
* Konténerkép: `mcr.microsoft.com/azuredocs/aci-helloworld` (Linux)

![Új tárolópéldány alapbeállításainak konfigurálása az Azure Portalon][aci-portal-03]

Ehhez a rövid útmutatóhoz az alapértelmezett beállításokat használja a nyilvános Microsoft-lemezkép `aci-helloworld` központi telepítéséhez. Ez a minta Linux-lemezkép egy node.js-ben írt kis webalkalmazást csomagol, amely statikus HTML-oldalt szolgál ki. Az Azure Container Registry, a Docker Hub vagy más beállításjegyzékekben tárolt saját tárolórendszerképeket is hozhat.

A **Hálózat lapon** adja meg a tároló **DNS-névcímkéjét.** A névnek egyedinek kell lennie az Azure-régióban, ahol létrehozza a tárolópéldányt. A tároló nyilvánosan elérhető az alábbi helyen: `<dns-name-label>.<region>.azurecontainer.io`. Ha „DNS-névcímke nem érhető el” hibaüzenetet kap, próbálkozzon másik DNS-névcímkével.

![Hálózati beállítások konfigurálása új tárolópéldányhoz az Azure Portalon][aci-portal-04]

Hagyja a többi beállítást az alapértelmezett értéken, majd válassza **a Véleményezés + create**lehetőséget.

Miután az érvényesítés befejeződött, egy összefoglalás jelenik meg a tároló beállításairól. Válassza a **Létrehozás lehetőséget** a tároló telepítési kérelmének elküldéséhez.

![Új tárolópéldány beállításainak összefoglalása az Azure Portalon][aci-portal-05]

A központi telepítés indításakor egy értesítés jelenik meg, amely jelzi, hogy a központi telepítés folyamatban van. Amikor a tárolócsoport üzembe helyezése megtörtént, egy újabb értesítés jelenik meg.

Nyissa meg a tárolócsoport áttekintését az **Erőforráscsoportok** > **myresourcegroup** > **mycontainer**elemre navigálva. Jegyezze fel a tárolópéldány teljes tartománynevét (**FQDN**) és **Állapotát**.

![Tárolócsoport áttekintése az Azure Portalon][aci-portal-06]

Ha az **Állapot***Fut* értékre vált, navigáljon a tároló teljes tartománynevére a böngészőjében.

![Az Azure Container Instances használatával üzembe helyezett alkalmazás képe a böngészőben][aci-portal-07]

Gratulálunk! Csupán néhány beállítás konfigurálásával üzembe helyezett egy nyilvánosan hozzáférhető alkalmazást az Azure Container Instances szolgáltatásban.

## <a name="view-container-logs"></a>Tárolónaplók megtekintése

A tárolópéldányok naplóinak megtekintése hasznos lehet a tárolóval vagy az azon futtatott alkalmazással kapcsolatos hibák elhárítása során.

A tároló naplóinak megtekintéséhez a **Beállítások**csoportban válassza a **Tárolók**lehetőséget, majd **a Naplók**lehetőséget. Látnia kell a HTTP GET kérést is, amely akkor jött létre, amikor megtekintette az alkalmazást a böngészőjében.

![Tárolónaplók az Azure Portalon][aci-portal-11]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután végzett a tárolóval, válassza ki az **Áttekintés** lehetőséget a *mycontainer* tárolópéldányhoz, majd válassza a **Törlés** lehetőséget.

![Tárolópéldány törlése az Azure Portalon][aci-portal-09]

Válassza az **Igen** lehetőséget, amikor a megerősítési párbeszédpanel megjelenik.

![Tárolópéldány törlésének megerősítése az Azure Portalon][aci-portal-10]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Azure-tároló példányt egy nyilvános Microsoft-lemezképből. Ha szeretne létrehozni és üzembe helyezni egy tárolórendszerképet egy privát Azure-tárolóregisztrációs adatbázisból, lépjen tovább az Azure Container Instances oktatóanyagára.

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