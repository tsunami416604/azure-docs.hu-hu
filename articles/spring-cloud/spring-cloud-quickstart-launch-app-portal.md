---
title: Rövid útmutató – Meglévő Azure Spring Cloud-alkalmazás indítása az Azure Portal használatával
description: Ebben a rövid útmutatóban üzembe helyezhet egy spring cloud-alkalmazást az Azure Spring Cloud szolgáltatásba az Azure Portal használatával.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 02/15/2020
ms.author: brendm
ms.openlocfilehash: 9cd59fdf81e9b5d56872d20c76e8ea177b3c8577
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79470894"
---
# <a name="quickstart-launch-an-existing-azure-spring-cloud-application-using-the-azure-portal"></a>Rövid útmutató: Meglévő Azure Spring Cloud-alkalmazás indítása az Azure Portalhasználatával

Ez a rövid útmutató bemutatja, hogyan telepíthet egy meglévő Spring Cloud-alkalmazást az Azure-ba. Az Azure Spring Cloud lehetővé teszi a Spring Cloud alapú mikroszolgáltatási alkalmazások egyszerű futtatását az Azure-ban. 

Az oktatóanyagban használt mintaalkalmazás-kódot a [GitHub-mintatárházban](https://github.com/Azure-Samples/PiggyMetrics)találja. Ha elkészült, a megadott mintaalkalmazás elérhető lesz az interneten, és készen áll az Azure Portalon keresztül történő kezelhető.

A rövid útmutatót követően megtudhatja, hogyan:

> [!div class="checklist"]
> * Szolgáltatáspéldány kiépítése
> * Példány konfigurációs kiszolgálójának beállítása
> * Mikroszolgáltatási alkalmazás létrehozása helyileg
> * Az egyes mikroszolgáltatások üzembe helyezése
> * Nyilvános végpont hozzárendelése az alkalmazáshoz

## <a name="prerequisites"></a>Előfeltételek

>[!Note]
> Az Azure Spring Cloud jelenleg nyilvános előzetes verzióként érhető el. A nyilvános előzetes verziólehetővé teszi az ügyfelek számára, hogy a hivatalos kiadás előtt kísérletezzenek az új funkciókkal.  A nyilvános előzetes verziójú funkciók és szolgáltatások nem éles környezetben való használatra valók.  Az előzetes verziók során nyújtott támogatással kapcsolatos további információkért tekintse át [a gyakori kérdéseket,](https://azure.microsoft.com/support/faq/) vagy nyújtson be [támogatási kérelmet](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) további információkért.

>[!TIP]
> Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat.  Előtelepített azure-eszközökkel rendelkezik, beleértve a Git, a JDK, a Maven és az Azure CLI legújabb verzióit. Ha be van jelentkezve az Azure-előfizetésbe, indítsa el az [Azure Cloud Shell-t](https://shell.azure.com) shell.azure.com.  Az Azure Cloud Shellről a [dokumentáció elolvasásával](../cloud-shell/overview.md) tudhat meg többet.

Az oktatóanyag elvégzéséhez:

1. [A Git telepítése](https://git-scm.com/)
2. [A JDK 8 telepítése](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [A Maven 3.0 vagy újabb telepítése](https://maven.apache.org/download.cgi)
4. [Az Azure CLI 2.0.67-es vagy újabb verziójának telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Regisztráció Azure-előfizetésre](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>Az Azure CLI bővítmény telepítése

Telepítse az Azure Spring Cloud bővítményt az Azure CLI-hez a következő paranccsal

```azurecli
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Szolgáltatáspéldány kiépítése az Azure Portalon

1. Egy új lapon nyissa meg az [Azure Portalt.](https://ms.portal.azure.com/) 

2. A felső keresőmezőben keresse meg az **Azure Spring Cloud**kifejezést.

3. Válassza ki az **Azure Spring Cloud** az eredmények közül.

 ![ASC ikon](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Az Azure Spring Cloud (Az Azure Spring Cloud) lapon kattintson a **+ Hozzáadás gombra.**

 ![ASC ikon](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Töltse ki az űrlapot az Azure Spring Cloud **Create** oldalon.  Vegye figyelembe a következő irányelveket:
    - **Előfizetés**: Válassza ki azt az előfizetést, amelyet ki szeretne fizetni az erőforrásért.  Győződjön meg arról, hogy ez az előfizetés felkerült az Azure Spring Cloud engedélyezési listájára.
    - **Erőforráscsoport**: Ajánlott eljárás új erőforráscsoportok létrehozása új erőforrásokhoz.
    - **Szolgáltatás részletei/neve:** Adja meg a szolgáltatáspéldány nevét.  A név nek 4 és 32 karakter közötti nek kell lennie, és csak kisbetűket, számokat és kötőjeleket tartalmazhat.  A szolgáltatásnév első karakterének betűnek, az utolsó karakternek pedig betűnek vagy számnak kell lennie.
    - **Hely**: Válassza ki a szolgáltatáspéldány helyét. A jelenleg támogatott helyek közé tartozik az USA keleti része, az USA nyugati része 2, Nyugat-Európa és Délkelet-Ázsia.

    ![ASC portál kezdete](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. A **következő** párbeszédpanel megnyitásához kattintson a Diagnosztikai beállítás fülre.

7. **A Naplók engedélyezése** *beállítási* beállítása az igényeknek megfelelően igen vagy *nem.*

    ![Naplók engedélyezése](media/spring-cloud-quickstart-launch-app-portal/diagnostic-setting.png)

8. Kattintson a **Kontúr** fülre.

9. **A nyomkövetés engedélyezése** beállítását az igényeknek megfelelően *igenre* vagy *nemre* állíthatja.  Ha a **Nyomkövetés engedélyezése** beállítását igenre állítja, válasszon ki egy meglévő alkalmazásbetekintést is, vagy hozzon létre egy újat. Az **Application Insights** specifikáció nélkül lesz egy érvényesítési hiba.


    ![Nyomkövetés](media/spring-cloud-quickstart-launch-app-portal/tracing.png)

10. Kattintson az **Áttekintés és létrehozás** elemre.

11. Ellenőrizze a specifikációkat, és kattintson a **Létrehozás gombra.**

Körülbelül 5 percet vesz igénybe a szolgáltatás üzembe helyezése.  Üzembe helyezése után megjelenik a szolgáltatáspéldány **áttekintése** lap.

> [!div class="nextstepaction"]
> [Belefutottam egy problémába.](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=provision)


## <a name="set-up-your-configuration-server"></a>A konfigurációs kiszolgáló beállítása

1. Nyissa meg a szolgáltatás **áttekintése** lapot, és válassza a **Config Server**lehetőséget.

2. Az **Alapértelmezett tárház** szakaszban állítsahttps://github.com/Azure-Samples/piggymetrics-configaz URI **értéket** " ".

3. A módosítások mentéséhez válassza az **Alkalmaz** elemet.

    ![Képernyőkép az ASC portálról](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

> [!div class="nextstepaction"]
> [Belefutottam egy problémába.](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=config-server)

## <a name="build-and-deploy-microservice-applications"></a>Mikroszolgáltatási alkalmazások létrehozása és üzembe helyezése

1. Nyisson meg egy [Azure Cloud Shellt,](https://shell.azure.com) és klónozza a mintaalkalmazás-tárházat a helyi számítógépre.  Itt először létrehozunk egy `source-code` ideiglenes könyvtárat, amelyet az alkalmazás klónozása előtt hívnak.

    ```console
    mkdir source-code
    cd source-code
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Készítsd el a klónozott csomagot.

    ```console
    cd piggymetrics
    mvn clean package -DskipTests
    ```

3. Nevek hozzárendelése az erőforráscsoporthoz és a szolgáltatáshoz. Győződjön meg arról, hogy az alábbi helyőrzőket az oktatóanyag korábbi részén kiépített erőforráscsoport nevével és szolgáltatásnevével helyettesítheti.

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

4. Hozza `gateway` létre az alkalmazást, és telepítse a JAR-fájlt.

    ```azurecli
    az spring-cloud app create -n gateway
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    ```

5. Ugyanezt a mintát `account-service` követve hozza létre a és `auth-service` az alkalmazásokat, és telepítse a JAR-fájlokat.

    ```azurecli
    az spring-cloud app create -n account-service
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app create -n auth-service
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

6. Az alkalmazások üzembe helyezése néhány percet vesz igénybe. Annak ellenőrzéséhez, hogy üzembe helyezte, nyissa meg az **Alkalmazások** panel az Azure Portalon. Meg kell jelennie egy sort mind a három alkalmazás.

> [!div class="nextstepaction"]
> [Belefutottam egy problémába.](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=deploy)

## <a name="assign-a-public-endpoint-to-gateway"></a>Nyilvános végpont hozzárendelése az átjáróhoz

1. Nyissa meg az **Alkalmazások** lapot a bal oldali menüben.

2. Válassza `gateway` ki az alkalmazást az Áttekintés lap **megjelenítéséhez.**

3. Válassza **a Végpont hozzárendelése** lehetőséget, ha nyilvános végpontot szeretne hozzárendelni az átjáróhoz. Ez eltarthat néhány percig.

    ![Képernyőkép az ASC portálról](media/spring-cloud-quickstart-launch-app-portal/portal-endpoint.png)

4. Adja meg a hozzárendelt nyilvános végpontot **(url-cím)** a böngészőbe a futó alkalmazás megtekintéséhez.

    ![Képernyőkép az ASC portálról](media/spring-cloud-quickstart-launch-app-portal/sample-app.png)

> [!div class="nextstepaction"]
> [Belefutottam egy problémába.](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=public-endpoint)

## <a name="next-steps"></a>További lépések

Ennek a rövid útmutatónak a segítségével megtanulta a következőket:

> [!div class="checklist"]
> * Szolgáltatáspéldány kiépítése
> * Példány konfigurációs kiszolgálójának beállítása
> * Mikroszolgáltatási alkalmazás létrehozása helyileg
> * Az egyes mikroszolgáltatások üzembe helyezése
> * Nyilvános végpont hozzárendelése az alkalmazásátjáróhoz

> [!div class="nextstepaction"]
> [Készítse elő az Azure Spring Cloud-alkalmazást a telepítéshez](spring-cloud-tutorial-prepare-app-deployment.md)

További minták érhetők el a GitHubon: [Azure Spring Cloud Samples.](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql)
