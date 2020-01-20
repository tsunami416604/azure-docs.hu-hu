---
title: Rövid útmutató – Azure Spring Cloud-alkalmazás elindítása a Azure Portal használatával
description: Ebben a rövid útmutatóban üzembe helyezhet egy minta alkalmazást az Azure Spring-felhőben a Azure Portal használatával.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 11/4/2019
ms.author: brendm
ms.openlocfilehash: f88319319b366ec8b726d5cf6cce540008497b57
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278906"
---
# <a name="quickstart-launch-an-azure-spring-cloud-application-using-the-azure-portal"></a>Gyors útmutató: Azure Spring Cloud-alkalmazás elindítása a Azure Portal használatával

Az Azure Spring Cloud lehetővé teszi, hogy könnyedén futtasson Spring Cloud-alapú Service-alkalmazásokat az Azure-ban.

Ez a rövid útmutató bemutatja, hogyan helyezhet üzembe egy meglévő Spring Cloud-alkalmazást az Azure-ban.  A jelen oktatóanyagban használt minta alkalmazás kódját a GitHub-mintákat tartalmazó [tárházban](https://github.com/Azure-Samples/PiggyMetrics)találja. Ha elkészült, a megadott minta alkalmazás elérhető lesz az interneten, és készen áll a Azure Portalon keresztül történő felügyeletre.

Ez a rövid útmutató a következőket ismerteti:

> [!div class="checklist"]
> * Szolgáltatási példány kiépítése
> * Konfigurációs kiszolgáló beállítása egy példányhoz
> * A Services-alkalmazások helyi létrehozása
> * Az egyes szolgáltatások üzembe helyezése
> * Nyilvános végpont kiosztása az alkalmazáshoz

## <a name="prerequisites"></a>Előfeltételek

>[!Note]
> Az Azure Spring Cloud jelenleg nyilvános előzetes verzióként érhető el. A nyilvános előzetes ajánlatok lehetővé teszik, hogy az ügyfelek a hivatalos kiadásuk előtt új funkciókkal kísérletezzenek.  A nyilvános előzetes verzió funkcióit és szolgáltatásait nem éles használatra szánták.  Az előzetes verziókra vonatkozó támogatással kapcsolatos további információkért tekintse meg a [gyakori kérdéseket](https://azure.microsoft.com/support/faq/) , vagy a [support Request](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) .

>[!TIP]
> Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat.  A közös Azure-eszközök előre telepítve vannak, beleértve a git, a JDK, a Maven és az Azure CLI legújabb verzióit. Ha bejelentkezett az Azure-előfizetésbe, indítsa el a [Azure Cloud shellt](https://shell.azure.com) a shell.Azure.com webhelyről.  A Azure Cloud Shellről a [dokumentáció beolvasásával](../cloud-shell/overview.md) tájékozódhat bővebben

Az oktatóanyag elvégzéséhez:

1. [A Git telepítése](https://git-scm.com/)
2. [A JDK 8 telepítése](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [A Maven 3,0-es vagy újabb verziójának telepítése](https://maven.apache.org/download.cgi)
4. [Az Azure CLI 2.0.67 vagy újabb verziójának telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Feliratkozás Azure-előfizetésre](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>Az Azure CLI-bővítmény telepítése

Telepítse az Azure CLI-hez készült Azure Spring Cloud-bővítményt az alábbi paranccsal

```Azure CLI
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Szolgáltatási példány kiépítése a Azure Portal

1. A böngészőben nyissa meg az [Azure Spring Cloud-ra mutató hivatkozást a Azure Portal](https://ms.portal.azure.com/#create/Microsoft.AppPlatform).

1. Töltse ki az űrlapot az Azure Spring Cloud **create** oldalon.  Vegye figyelembe a következő irányelveket:
    - Szolgáltatás neve: adja meg a szolgáltatás példányának nevét.  A névnek 4 – 32 karakter hosszúnak kell lennie, és csak kisbetűket, számokat és kötőjeleket tartalmazhat.  A szolgáltatás nevének első karakterének betűnek kell lennie, és az utolsó karakternek betűnek vagy számnak kell lennie.
    - Előfizetés: válassza ki azt az előfizetést, amelyet számlázni szeretne az erőforráshoz.  Győződjön meg arról, hogy ez az előfizetés hozzá lett adva az Azure Spring Cloud engedélyezési listájához.
    - Erőforráscsoport: új erőforráscsoportok létrehozása új erőforrásokhoz az ajánlott eljárás.
    - Hely: válassza ki a szolgáltatás példányának helyét. A jelenleg támogatott helyszínek közé tartozik az USA keleti régiója, USA 2. nyugati régiója, Nyugat-Európa és Délkelet-Ázsia.

1. Kattintson **a felülvizsgálat és létrehozás**gombra.

1. Ellenőrizze a specifikációkat, majd kattintson a **Létrehozás**gombra.

A szolgáltatás üzembe helyezése körülbelül 5 percet vesz igénybe.  A telepítés után megjelenik a szolgáltatási példány **Áttekintés** lapja.

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=provision)


## <a name="set-up-your-configuration-server"></a>A konfigurációs kiszolgáló beállítása

1. Lépjen a szolgáltatás **áttekintése** lapra, és válassza a **konfigurációs kiszolgáló**lehetőséget.

1. Az **alapértelmezett adattár** szakaszban állítsa be az **URI** -t a "https\://GitHub.com/Azure-Samples/piggymetrics" értékre.

1. **Címke** beállítása a "config" értékre.

1. A módosítások mentéséhez válassza az **Alkalmaz** elemet.

    ![Képernyőfelvétel az ASC-portálról](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=config-server)

## <a name="build-and-deploy-microservice-applications"></a>Service-alkalmazások készítése és üzembe helyezése

1. Nyisson meg egy [Azure Cloud Shell](https://shell.azure.com) , és klónozása a minta alkalmazás-tárházat a helyi gépre.  Először hozzunk létre egy `source-code` nevű ideiglenes könyvtárat az alkalmazás klónozása előtt.

    ```azurecli
    mkdir source-code
    cd source-code
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

1. Hozza létre a klónozott csomagot.

    ```azurecli
    cd piggymetrics
    mvn clean package -DskipTests
    ```
1. Neveket rendelhet hozzá az erőforráscsoporthoz és a szolgáltatáshoz. Ügyeljen arra, hogy az alábbi helyőrzőket az oktatóanyag korábbi részében kiépített erőforráscsoport-névvel és szolgáltatás nevével helyettesítse.

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

1. Hozza létre a `gateway` alkalmazást, és telepítse a JAR-fájlt.

    ```azurecli
    az spring-cloud app create -n gateway
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    ```

1. Ugyanezt a mintát követve hozza létre a `account-service` és `auth-service` alkalmazásokat, és telepítse a JAR-fájljait.

    ```azurecli
    az spring-cloud app create -n account-service
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app create -n auth-service
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

1. Az alkalmazások üzembe helyezésének befejezése néhány percet vesz igénybe. Annak ellenőrzéséhez, hogy telepítve vannak-e, nyissa meg az **alkalmazások** panelt a Azure Portal. A három alkalmazás sorát kell látnia.

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=deploy)

## <a name="assign-a-public-endpoint-to-gateway"></a>Nyilvános végpont társítása az átjáróhoz

1. Nyissa meg a bal oldali menü **alkalmazások** lapját.

1. Válassza ki a `gateway` alkalmazást az **Áttekintés** lap megjelenítéséhez.

1. Válassza a **tartomány kiosztása** lehetőséget egy nyilvános végpontnak az átjáróhoz való hozzárendeléséhez. Ez eltarthat néhány percig.

    ![Képernyőfelvétel az ASC-portálról](media/spring-cloud-quickstart-launch-app-portal/portal-endpoint.png)

1. A futó alkalmazás megtekintéséhez adja meg a hozzárendelt nyilvános végpontot (címkével ellátott **URL-címet**) a böngészőben.

    ![Képernyőfelvétel az ASC-portálról](media/spring-cloud-quickstart-launch-app-portal/sample-app.png)

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Következő lépések

Ennek a rövid útmutatónak a segítségével megtanulta a következőket:

> [!div class="checklist"]
> * Szolgáltatási példány kiépítése
> * Konfigurációs kiszolgáló beállítása egy példányhoz
> * A Services-alkalmazások helyi létrehozása
> * Az egyes szolgáltatások üzembe helyezése
> * Nyilvános végpont kiosztása az Application Gateway számára

> [!div class="nextstepaction"]
> [Az Azure Spring Cloud-alkalmazás előkészítése az üzembe helyezéshez](spring-cloud-tutorial-prepare-app-deployment.md)

További minták érhetők el a GitHubon: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
