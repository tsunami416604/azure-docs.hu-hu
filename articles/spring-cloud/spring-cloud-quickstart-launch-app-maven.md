---
title: 'Gyors útmutató: alkalmazás elindítása a Maven használatával – Azure Spring Cloud'
description: Minta alkalmazás elindítása a Maven használatával
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/05/2019
ms.author: v-vasuke
ms.openlocfilehash: e773b997cca3fa9a1f11fec2ac449e1fc11c5364
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554567"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-by-using-the-maven-plug-in"></a>Gyors útmutató: Azure Spring Cloud-alkalmazás elindítása a Maven beépülő modullal

Az Azure Spring Cloud Maven beépülő modullal egyszerűen létrehozhatja és frissítheti Azure Spring Cloud Service-alkalmazásait. Egy konfiguráció elődefiniálásával alkalmazásokat telepíthet a meglévő Azure Spring Cloud Service-be. Ebben a cikkben egy PiggyMetrics nevű minta alkalmazást használ a funkció bemutatásához.

>[!Note]
> A rövid útmutató elkezdése előtt győződjön meg arról, hogy az Azure-előfizetés hozzáfér az Azure Spring Cloudhoz. Előzetes verzióként meghívjuk Önt, hogy felvegye velünk a kapcsolatot, hogy fel tudja venni az előfizetését az engedélyezési listára. Ha szeretné felfedezni az Azure Spring Cloud képességeit, töltse ki és küldje be az [Azure Spring Cloud (privát előzetes verzió) érdeklődési formáját](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-LA2geqX-ZLhi-Ado1LD3tUNDk2VFpGUzYwVEJNVkhLRlcwNkZFUFZEUS4u).

>[!TIP]
> A Azure Cloud Shell egy ingyenes interaktív felület, amelyet a cikkben szereplő parancsok futtatására használhat. A közös Azure-eszközök előre telepítve vannak, beleértve a git legújabb verzióit, a Java Development Kit (JDK), a Maven és az Azure CLI-t. Ha bejelentkezett az Azure-előfizetésbe, indítsa el [Azure Cloud Shell](https://shell.azure.com). További információ: [Azure Cloud Shell áttekintése](../cloud-shell/overview.md).

A gyorsútmutató elvégzéséhez:

1. [Telepítse a Git szoftvert](https://git-scm.com/).
2. A [JDK 8 telepítése](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable).
3. [Telepítse a Maven 3,0-es vagy újabb verzióját](https://maven.apache.org/download.cgi).
4. [Telepítse az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)-t.
5. [Regisztráljon egy ingyenes Azure-előfizetésre](https://azure.microsoft.com/free/).

## <a name="install-the-azure-cli-extension"></a>Az Azure CLI-bővítmény telepítése

Telepítse az Azure CLI-hez készült Azure Spring Cloud-bővítményt az alábbi paranccsal:

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Szolgáltatási példány kiépítése a Azure Portal

1. A böngészőben nyissa meg a [Azure Portal](https://portal.azure.com), és jelentkezzen be a fiókjába.

1. Keresse meg és válassza ki az **Azure Spring Cloud**elemet. 
1. Az Áttekintés lapon válassza a **Létrehozás**lehetőséget, majd tegye a következőket:  

    a. A **szolgáltatás neve** mezőben adja meg a szolgáltatás példányának nevét. A névnek 4 – 32 karakter hosszúnak kell lennie, és csak kisbetűket, számokat és kötőjeleket tartalmazhat. A szolgáltatás nevének első karakterének betűnek kell lennie, és az utolsó karakternek betűnek vagy számnak kell lennie.  

    b. Az **előfizetés** legördülő listában válassza ki azt az előfizetést, amelyet ki szeretne számlázni ehhez az erőforráshoz. Győződjön meg arról, hogy az előfizetés hozzá lett adva az Azure Spring Cloud engedélyezési listájához.  

    c. Az **erőforráscsoport** mezőben hozzon létre egy új erőforráscsoportot. Az új erőforrásokhoz tartozó erőforráscsoportok létrehozása ajánlott eljárás.  

    d. A **hely** legördülő listában válassza ki a szolgáltatás példányának helyét. A jelenleg támogatott helyszínek közé tartozik az USA keleti régiója, USA 2. nyugati régiója, Nyugat-Európa és Délkelet-Ázsia.
    
A szolgáltatás üzembe helyezése körülbelül 5 percet vesz igénybe. A szolgáltatás üzembe helyezését követően megjelenik a szolgáltatási példány **Áttekintés** lapja.

## <a name="set-up-your-configuration-server"></a>A konfigurációs kiszolgáló beállítása

1. A szolgáltatás **áttekintése** lapon válassza a **konfigurációs kiszolgáló**lehetőséget.
1. Az **alapértelmezett adattár** szakaszban állítsa be az **URI** - **t https://github.com/Azure-Samples/piggymetrics re**, állítsa be a **címkét** a **config**értékre, majd kattintson az **alkalmaz** gombra a módosítások mentéséhez.

## <a name="clone-and-build-the-sample-application-repository"></a>A minta alkalmazás-adattár klónozása és összeállítása

1. A git-tárház klónozásához futtassa a következő parancsot:

    ```azurecli
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Módosítsa a könyvtárat, és hozza létre a projektet a következő parancs futtatásával:

    ```azurecli
    cd PiggyMetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-and-deploy-the-azure-spring-cloud-configuration"></a>Az Azure Spring Cloud konfigurációjának előállítása és üzembe helyezése

1. Ha engedélyezni kívánja a Mavent az Azure Spring Cloudtel való együttműködéshez, adja hozzá a következő kódot a *Pom. XML* vagy a *Settings. XML* fájlhoz.

    ```xml
    <pluginRepositories>
      <pluginRepository>
        <id>maven.snapshots</id>
        <name>Maven Central Snapshot Repository</name>
        <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
        <releases>
          <enabled>false</enabled>
        </releases>
        <snapshots>
          <enabled>true</enabled>
        </snapshots>
      </pluginRepository>
    </pluginRepositories>
    ```

1. Konfiguráció létrehozása a következő parancs futtatásával:

    ```azurecli
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:config
    ```

    a. Válassza ki a modulokat `gateway`, `auth-service` és `account-service`.

    b. Válassza ki az előfizetését és az Azure Spring Cloud Service-fürtöt.

    c. A megadott projektek listájában adja meg azt a számot, amely megfelel a `gateway`nek, hogy nyilvános hozzáférést adjon.
    
    d. Erősítse meg a konfigurációt.

1. Telepítse az alkalmazásokat a következő parancs használatával:

   ```azurecli
   mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:deploy
   ```

1. A PiggyMetrics az előző parancs kimenetében megadott URL-cím használatával érheti el.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy Spring Cloud-alkalmazást telepített egy Maven-tárházból. Ha többet szeretne megtudni az Azure Spring Cloud-ról, folytassa az alkalmazás telepítésre való előkészítésének oktatóanyagával.

> [!div class="nextstepaction"]
> [Az Azure Spring Cloud-alkalmazás előkészítése az üzembe helyezéshez](spring-cloud-tutorial-prepare-app-deployment.md)
