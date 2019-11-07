---
title: 'Gyors útmutató: alkalmazás elindítása a Maven használatával – Azure Spring Cloud'
description: Minta alkalmazás elindítása Maven használatával
author: jpconnock
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: 440dac3ff26c838458f91e453e2c62fc0953d4f6
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686184"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-using-the-maven-plug-in"></a>Gyors útmutató: Azure Spring Cloud-alkalmazás elindítása a Maven beépülő modullal

Az Azure Spring Cloud Maven beépülő modullal egyszerűen létrehozhatja és frissítheti az Azure Spring Cloud-alkalmazásait. Egy konfiguráció elődefiniálásával alkalmazásokat telepíthet a meglévő Azure Spring Cloud Service-be. Ebben a cikkben egy PiggyMetrics nevű minta alkalmazást használ a funkció bemutatásához.

Ez a rövid útmutató a következőket ismerteti:

> [!div class="checklist"]
> * Szolgáltatási példány kiépítése
> * Konfigurációs kiszolgáló beállítása egy példányhoz
> * A Services-alkalmazások helyi klónozása és kiépítése
> * Az egyes szolgáltatások üzembe helyezése
> * Nyilvános végpont kiosztása az alkalmazáshoz

>[!Note]
> > Az Azure Spring Cloud jelenleg nyilvános előzetes verzióként érhető el. A nyilvános előzetes ajánlatok lehetővé teszik, hogy az ügyfelek a hivatalos kiadásuk előtt új funkciókkal kísérletezzenek.  A nyilvános előzetes verzió funkcióit és szolgáltatásait nem éles használatra szánták.  Az előzetes verziókra vonatkozó támogatással kapcsolatos további információkért írjon be egy [support Request](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).

>[!TIP]
> A Azure Cloud Shell egy ingyenes interaktív felület, amelyet a cikkben szereplő parancsok futtatására használhat. A közös Azure-eszközök előre telepítve vannak, beleértve a git legújabb verzióit, a Java Development Kit (JDK), a Maven és az Azure CLI-t. Ha bejelentkezett az Azure-előfizetésbe, indítsa el [Azure Cloud Shell](https://shell.azure.com). További információ: [Azure Cloud Shell áttekintése](../cloud-shell/overview.md).

A gyorsútmutató elvégzéséhez:

1. [Telepítse a Git szoftvert](https://git-scm.com/).
2. A [JDK 8 telepítése](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable).
3. [Telepítse a Maven 3,0-es vagy újabb verzióját](https://maven.apache.org/download.cgi).
4. [Telepítse az Azure CLI 2.0.67 vagy újabb verzióját](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
5. [Regisztráljon egy ingyenes Azure-előfizetésre](https://azure.microsoft.com/free/).

## <a name="install-the-azure-cli-extension"></a>Az Azure CLI-bővítmény telepítése

Telepítse az Azure CLI-hez készült Azure Spring Cloud-bővítményt az alábbi paranccsal:

```Azure CLI
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Szolgáltatási példány kiépítése a Azure Portal

1. A böngészőben nyissa meg az [Azure Spring Cloud-ra mutató hivatkozást a Azure Portalban](https://ms.portal.azure.com/#create/Microsoft.AppPlatform), és jelentkezzen be a fiókjába.

1. Válassza a **Létrehozás** lehetőséget az **Áttekintés** lapon a létrehozás párbeszédpanel megnyitásához.

1. Adja meg a minta alkalmazás **projekt részleteit** a következőképpen:

    1. Válassza ki azt az **előfizetést** , amelyhez az alkalmazást társítani kívánja.
    1. Válasszon ki vagy hozzon létre egy erőforráscsoportot az alkalmazáshoz. Javasoljuk, hogy hozzon létre egy új erőforráscsoportot.  Az alábbi példa egy `myspringservice`nevű új erőforráscsoportot mutat be.
    1. Adja meg az új Azure Spring Cloud szolgáltatás nevét.  A névnek 4 – 32 karakter hosszúnak kell lennie, és csak kisbetűket, számokat és kötőjeleket tartalmazhat. A szolgáltatás nevének első karakterének betűnek kell lennie, és az utolsó karakternek betűnek vagy számnak kell lennie.  Az alábbi példában szereplő szolgáltatás neve `contosospringcloud`.
    1. Válasszon egy helyet az alkalmazás számára a megadott beállítások közül.  Ebben a példában a `East US`lehetőséget választjuk.
    1. Az új szolgáltatás összefoglalásának áttekintéséhez válassza a **felülvizsgálat + létrehozás** lehetőséget.  Ha úgy tűnik, hogy minden helyes, válassza a **Létrehozás**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![válassza a felülvizsgálat + létrehozás](media/maven-qs-review-create.jpg)

A szolgáltatás üzembe helyezése körülbelül 5 percet vesz igénybe. A szolgáltatás telepítése után válassza az Ugrás az **erőforráshoz** lehetőséget, és megjelenik a szolgáltatási példány **Áttekintés** lapja.

## <a name="set-up-your-configuration-server"></a>A konfigurációs kiszolgáló beállítása

1. A szolgáltatás **áttekintése** lapon válassza a **konfigurációs kiszolgáló**lehetőséget.
1. Az **alapértelmezett adattár** szakaszban állítsa be az **URI** - **t https://github.com/Azure-Samples/piggymetricsre** , állítsa be a **címkét** a **config**értékre, majd kattintson az **alkalmaz** gombra a módosítások mentéséhez.

    > [!div class="mx-imgBorder"]
    > ![a konfigurációs beállítások megadása és alkalmazása](media/maven-qs-apply-config.jpg)

## <a name="clone-and-build-the-sample-application-repository"></a>A minta alkalmazás-adattár klónozása és összeállítása

1. Indítsa el a [Azure Cloud Shell](https://shell.azure.com).

1. A git-tárház klónozásához futtassa a következő parancsot:

    ```azurecli
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Módosítsa a könyvtárat, és hozza létre a projektet a következő parancs futtatásával:

    ```azurecli
    cd piggymetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-configurations-and-deploy-to-the-azure-spring-cloud"></a>Konfigurációk készítése és üzembe helyezése az Azure Spring Cloud-ban

1. Konfigurációk előállításához futtassa a következő parancsot a szülő POM-t tartalmazó PiggyMetrics gyökérkönyvtárában:

    ```azurecli
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:1.0.0:config
    ```

    a. Válassza ki a modulokat `gateway`, `auth-service` és `account-service`.

    b. Válassza ki az előfizetését és az Azure Spring Cloud Service-fürtöt.

    c. A megadott projektek listájában adja meg azt a számot, amely megfelel a `gateway`nek, hogy nyilvános hozzáférést adjon.
    
    d. Erősítse meg a konfigurációt.

1. A POM mostantól tartalmazza a beépülő modul függőségeit és konfigurációit. Telepítse az alkalmazásokat a következő parancs használatával:

   ```azurecli
   mvn azure-spring-cloud:deploy
   ```

1. Az üzembe helyezés befejezése után a PiggyMetrics az előző parancs kimenetében megadott URL-címen keresztül érheti el.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy Spring Cloud-alkalmazást telepített egy Maven-tárházból. Ha többet szeretne megtudni az Azure Spring Cloud-ról, folytassa az alkalmazás telepítésre való előkészítésének oktatóanyagával.

> [!div class="nextstepaction"]
> [Az Azure Spring Cloud-alkalmazás előkészítése az üzembe helyezéshez](spring-cloud-tutorial-prepare-app-deployment.md)
> további információ az Azure-beli [Maven beépülő moduljairól](https://github.com/microsoft/azure-maven-plugin)
