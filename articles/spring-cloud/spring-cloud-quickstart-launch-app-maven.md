---
title: Rövid útmutató – Alkalmazás indítása a Maven használatával az Azure Spring Cloud segítségével
description: Ebben a rövid útmutatóban indítson el egy mintaalkalmazást a Maven
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: d1e6d6db1465b20f1f32a8ffb2f978d0a04a1033
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79470843"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-using-the-maven-plug-in"></a>Rövid útmutató: Elindíthat egy Azure Spring Cloud alkalmazást a Maven beépülő modul használatával

Az Azure Spring Cloud Maven beépülő modul használatával egyszerűen létrehozhatja és frissítheti az Azure Spring Cloud-alkalmazásokat. A konfiguráció előzetes definiálásával alkalmazásokat telepíthet a meglévő Azure Spring Cloud szolgáltatásra. Ebben a cikkben a PiggyMetrics nevű mintaalkalmazást használja a funkció bemutatásához.

A rövid útmutatót követően megtudhatja, hogyan:

> [!div class="checklist"]
> * Szolgáltatáspéldány kiépítése
> * Konfigurációs kiszolgáló beállítása egy példányhoz
> * Mikroszolgáltatások alkalmazás ának klónozása és létrehozása helyileg
> * Az egyes mikroszolgáltatások üzembe helyezése
> * Nyilvános végpont hozzárendelése az alkalmazáshoz

>[!Note]
> Az Azure Spring Cloud jelenleg nyilvános előzetes verzióként érhető el. A nyilvános előzetes verziólehetővé teszi az ügyfelek számára, hogy a hivatalos kiadás előtt kísérletezzenek az új funkciókkal.  A nyilvános előzetes verziójú funkciók és szolgáltatások nem éles környezetben való használatra valók.  Az előzetes verziók során nyújtott támogatással kapcsolatos további információkért tekintse át [a gyakori kérdéseket,](https://azure.microsoft.com/support/faq/) vagy nyújtson be [támogatási kérelmet](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) további információkért.


>[!TIP]
> Az Azure Cloud Shell egy ingyenes interaktív rendszerhéj, amely segítségével futtathatja a parancsokat ebben a cikkben. Előtelepített azure-eszközökkel rendelkezik, beleértve a Git legújabb verzióját, a Java Development Kit (JDK), a Maven és az Azure CLI legújabb verzióit. Ha be van jelentkezve azure-előfizetésébe, indítsa el az [Azure Cloud Shell](https://shell.azure.com)t. További információ: [Az Azure Cloud Shell áttekintése.](../cloud-shell/overview.md)

Az oktatóanyag elvégzéséhez:

1. [Telepítse a Git](https://git-scm.com/)- alkalmazást.
2. [Telepítse a JDK 8-at](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable).
3. [Telepítse a Maven 3.0-s vagy újabb telepítését.](https://maven.apache.org/download.cgi)
4. [Regisztráljon egy ingyenes Azure-előfizetésre.](https://azure.microsoft.com/free/)

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Szolgáltatáspéldány kiépítése az Azure Portalon

1. Egy webböngészőben nyissa meg [ezt a hivatkozást az Azure Spring Cloud](https://ms.portal.azure.com/#create/Microsoft.AppPlatform)az Azure Portalon , és jelentkezzen be a fiókjába.

1. Adja meg a **projekt részleteit** a mintaalkalmazáshoz az alábbiak szerint:

    1. Válassza ki azt az **Előfizetést,** amelyhez az alkalmazás társítva lesz.
    1. Jelöljön ki vagy hozzon létre erőforráscsoportot az alkalmazáshoz. Javasoljuk, hogy hozzon létre egy új erőforráscsoportot.  Az alábbi példa egy új `myspringservice`erőforráscsoportot mutat be, amelynek neve .
    1. Adja meg az új Azure Spring Cloud szolgáltatás nevét.  A név nek 4 és 32 karakter közötti nek kell lennie, és csak kisbetűket, számokat és kötőjeleket tartalmazhat. A szolgáltatásnév első karakterének betűnek, az utolsó karakternek pedig betűnek vagy számnak kell lennie.  Az alábbi példában található `contosospringcloud`szolgáltatás neve .
    1. A megadott lehetőségek közül válassza ki az alkalmazás helyét.  Ebben a példában `East US`a lehetőséget választjuk.
    1. Válassza **a Véleményezés + létrehozás** lehetőséget az új szolgáltatás összegzésének áttekintéséhez.  Ha minden jól néz ki, válassza a **Létrehozás gombot.**

    > [!div class="mx-imgBorder"]
    > ![Válassza a Véleményezés + létrehozás lehetőséget](media/maven-qs-review-create.jpg)

A szolgáltatás üzembe helyezése körülbelül 5 percet vesz igénybe. A szolgáltatás üzembe helyezése után válassza az **Ugrás az erőforráshoz** lehetőséget, és megjelenik a szolgáltatáspéldány **áttekintése** lap.

> [!div class="nextstepaction"]
> [Belefutottam egy problémába.](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=provision)

## <a name="set-up-your-configuration-server"></a>A konfigurációs kiszolgáló beállítása

1. A szolgáltatás **áttekintése** lapon válassza a **Config Server**lehetőséget.
1. Az **Alapértelmezett tárház** szakaszban **https://github.com/Azure-Samples/piggymetrics-config**állítsa az **URI-t** a értékre, majd válassza az **Alkalmaz** lehetőséget a módosítások mentéséhez.

    > [!div class="mx-imgBorder"]
    > ![Konfigurációs beállítások definiálása és alkalmazása](media/maven-qs-apply-config.jpg)

> [!div class="nextstepaction"]
> [Belefutottam egy problémába.](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=config-server)

## <a name="clone-and-build-the-sample-application-repository"></a>A mintaalkalmazás-tárház klónozása és létrehozása

1. Indítsa el az [Azure Cloud Shell](https://shell.azure.com)t.

1. Klónozza a Git-tárházat a következő parancs futtatásával:

    ```console
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Módosítsa a könyvtárat, és építse fel a projektet a következő parancs futtatásával:

    ```console
    cd piggymetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-configurations-and-deploy-to-the-azure-spring-cloud"></a>Konfigurációk létrehozása és üzembe helyezés az Azure Spring Cloud szolgáltatásban

1. Konfigurációk at hozhat létre a következő parancs futtatásával a szülő POM-ot tartalmazó PiggyMetrics gyökérmappájában:

    ```console
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:1.0.0:config
    ```

    a. Válassza ki `gateway`a`auth-service`modulokat , és `account-service`a lehetőséget.

    b. Válassza ki az előfizetést és az Azure Spring Cloud szolgáltatásfürtöt.

    c. A megadott projektek listájában adja meg azt `gateway` a számot, amely megfelel a nyilvános hozzáférés hez.
    
    d. Erősítse meg a konfigurációt.

1. A POM most már tartalmazza a plugin függőségek és konfigurációk. Telepítse az alkalmazásokat a következő paranccsal:

   ```console
   mvn azure-spring-cloud:deploy
   ```

1. Miután a központi telepítés befejeződött, elérheti a PiggyMetrics az előző parancs kimenetében megadott URL-címet használva.

> [!div class="nextstepaction"]
> [Belefutottam egy problémába.](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=deploy)

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban üzembe helyezett egy Spring Cloud-alkalmazást egy Maven-tárházból. Ha többet szeretne megtudni az Azure Spring Cloudról, folytassa az alkalmazás üzembe helyezésre való előkészítéséről szóló oktatóanyaggal.

> [!div class="nextstepaction"]
> [Az Azure Spring Cloud-alkalmazás előkészítése az üzembe helyezéshez](spring-cloud-tutorial-prepare-app-deployment.md)
> [További információ az Azure-hoz készült Maven beépülő modulokról](https://github.com/microsoft/azure-maven-plugin)

További minták érhetők el a GitHubon: [Azure Spring Cloud Samples.](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql)
