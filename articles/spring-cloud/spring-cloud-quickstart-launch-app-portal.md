---
title: Rövid útmutató – meglévő Azure Spring Cloud-alkalmazás elindítása a Azure Portal használatával
description: Ebben a rövid útmutatóban egy Spring Cloud-alkalmazást helyezhet üzembe az Azure Spring-felhőben a Azure Portal használatával.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 02/15/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 163940095c4a3a25123706ca47a7d059897cad20
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89046867"
---
# <a name="quickstart-launch-an-existing-azure-spring-cloud-application-using-the-azure-portal"></a>Gyors útmutató: meglévő Azure Spring Cloud-alkalmazás elindítása a Azure Portal használatával

Ez a rövid útmutató bemutatja, hogyan helyezhet üzembe egy meglévő Spring Cloud-alkalmazást az Azure-ban. Az Azure Spring Cloud lehetővé teszi, hogy könnyedén futtasson Spring Cloud-alapú Service-alkalmazásokat az Azure-ban. 

A példa futtatása előtt próbálja ki az [alapszintű](spring-cloud-quickstart.md)rövid útmutatót.

A jelen oktatóanyagban használt minta alkalmazás kódját a GitHub-mintákat tartalmazó [tárházban](https://github.com/Azure-Samples/PiggyMetrics)találja. Ha elkészült, a megadott minta alkalmazás elérhető lesz az interneten, és készen áll a Azure Portalon keresztül történő felügyeletre.

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

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Szolgáltatási példány kiépítése a Azure Portal

1. Az új lapon nyissa meg a [Azure Portal](https://ms.portal.azure.com/). 

2. A felső keresőmezőbe keressen az **Azure Spring Cloud**kifejezésre.

3. Válassza az **Azure Spring Cloud** lehetőséget az eredmények közül.

 ![ASC kezdete](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Az Azure Spring Cloud oldalon kattintson a **+ Hozzáadás**gombra.

 ![ASC Hozzáadás](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Töltse ki az űrlapot az Azure Spring Cloud **create** oldalon.  Vegye figyelembe a következő irányelveket:
    - **Előfizetés**: válassza ki azt az előfizetést, amelyet számlázni szeretne az erőforráshoz.  Győződjön meg arról, hogy ez az előfizetés hozzá lett adva az Azure Spring Cloud engedélyezési listájához.
    - **Erőforráscsoport**: új erőforráscsoportok létrehozása új erőforrásokhoz az ajánlott eljárás.
    - **Szolgáltatás részletei/név**: adja meg a szolgáltatás példányának nevét.  A névnek 4 – 32 karakter hosszúnak kell lennie, és csak kisbetűket, számokat és kötőjeleket tartalmazhat.  A szolgáltatás nevének első karakterének betűnek kell lennie, és az utolsó karakternek betűnek vagy számnak kell lennie.
    - **Hely**: válassza ki a szolgáltatás példányának helyét. A jelenleg támogatott helyszínek közé tartozik az USA keleti régiója, USA 2. nyugati régiója, Nyugat-Európa és Délkelet-Ázsia.

    ![ASC-portál indítása](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Kattintson a **diagnosztikai beállítások** lapra a következő párbeszédpanel megnyitásához.

7. Beállíthatja, hogy a **naplók engedélyezése** *Igen* vagy *nem* a követelmények szerint történjen.

    ![Naplók engedélyezése](media/spring-cloud-quickstart-launch-app-portal/diagnostic-setting.png)

8. Kattintson a **nyomkövetés** fülre.

9. A **nyomkövetés engedélyezése** beállítást *Igen* vagy *nem* értékre állíthatja a követelmények szerint.  Ha a **nyomkövetés engedélyezése** beállítást Igen értékre állítja, válasszon ki egy meglévő alkalmazás-betekintést, vagy hozzon létre egy újat. A **Application Insights** specifikáció nélkül érvényesítési hiba történik.


    ![Nyomkövetési nézet](media/spring-cloud-quickstart-launch-app-portal/tracing.png)

10. Kattintson az **Áttekintés és létrehozás** elemre.

11. Ellenőrizze a specifikációkat, majd kattintson a **Létrehozás**gombra.

A szolgáltatás üzembe helyezése körülbelül 5 percet vesz igénybe.  A telepítés után megjelenik a szolgáltatási példány **Áttekintés** lapja.

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=provision)


## <a name="set-up-your-configuration-server"></a>A konfigurációs kiszolgáló beállítása

1. Lépjen a szolgáltatás **áttekintése** lapra, és válassza a **konfigurációs kiszolgáló**lehetőséget.

2. Az **alapértelmezett adattár** szakaszban állítsa be az **URI** -t "" értékre https://github.com/Azure-Samples/piggymetrics-config .

3. A módosítások mentéséhez válassza az **Alkalmaz** elemet.

    ![ASC-portál](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=config-server)

## <a name="build-and-deploy-microservice-applications"></a>Service-alkalmazások készítése és üzembe helyezése

1. Nyisson meg egy [Azure Cloud Shell](https://shell.azure.com) vagy a helyi rendszerhéjt az Azure CLI-vel. Először létre kell hozni egy ideiglenes könyvtárat, amely `source-code` a minta alkalmazás klónozása előtt lett meghívva.

    ```console
    mkdir source-code
    cd source-code
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Hozza létre a klónozott csomagot.

    ```console
    cd piggymetrics
    mvn clean package -DskipTests
    ```

3. Telepítse az Azure CLI-hez készült Azure Spring Cloud-bővítményt az alábbi paranccsal

    ```azurecli
    az extension add --name spring-cloud
    ```

4. Neveket rendelhet hozzá az erőforráscsoporthoz és a szolgáltatáshoz. Ügyeljen arra, hogy az alábbi helyőrzőket az oktatóanyag korábbi részében kiépített erőforráscsoport-névvel és szolgáltatás nevével helyettesítse.

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

5. Hozza létre az `gateway` alkalmazást, és telepítse a jar-fájlt.

    A Spring Cloud bővítmény használatával hozza létre az alkalmazást:

    ```azurecli
    az spring-cloud app create -n gateway
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    ```

6. Ugyanezt a mintát követve hozza létre a `account-service` és az `auth-service` alkalmazásokat, és telepítse a jar-fájljaikat.

    ```azurecli
    az spring-cloud app create -n account-service
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app create -n auth-service
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

7. Az alkalmazások üzembe helyezésének befejezése néhány percet vesz igénybe. Annak ellenőrzéséhez, hogy telepítve vannak-e, nyissa meg az **alkalmazások** panelt a Azure Portal. A három alkalmazás sorát kell látnia.

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=deploy)

## <a name="assign-a-public-endpoint-to-gateway"></a>Nyilvános végpont társítása az átjáróhoz

1. Nyissa meg a bal oldali menü **alkalmazások** lapját.

2. Válassza ki az `gateway` alkalmazást az **Áttekintés** oldal megjelenítéséhez.

3. Válassza a **végpont társítása** lehetőséget egy nyilvános végpontnak az átjáróhoz való hozzárendeléséhez. Ez eltarthat néhány percig.

    ![ASC-portál végpontja](media/spring-cloud-quickstart-launch-app-portal/portal-endpoint.png)

4. A futó alkalmazás megtekintéséhez adja meg a hozzárendelt nyilvános végpontot (címkével ellátott **URL-címet**) a böngészőben.

    ![ASC-portál minta alkalmazás](media/spring-cloud-quickstart-launch-app-portal/sample-app.png)

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
