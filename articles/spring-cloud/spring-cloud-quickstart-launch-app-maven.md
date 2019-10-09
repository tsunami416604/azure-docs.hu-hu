---
title: 'Gyors útmutató: Alkalmazás elindítása a Maven használatával – Azure Spring Cloud'
description: Minta alkalmazás elindítása a Maven használatával
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/05/2019
ms.author: v-vasuke
ms.openlocfilehash: d2b9592656d00b38c18b8c9e07540ccb5913766c
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72039055"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-using-the-maven-plug-in"></a>Gyors útmutató: Azure Spring Cloud-alkalmazás elindítása a Maven beépülő modullal

Az Azure Spring Cloud Maven beépülő modulja lehetővé teszi az Azure Spring Cloud Service-alkalmazások egyszerű létrehozását és frissítését. A konfiguráció előzetes definiálásával alkalmazásokat telepíthet a meglévő Azure Spring Cloud Service-be. Ebben a cikkben egy PiggyMetrics nevű minta alkalmazást használunk a funkció bemutatásához.

>[!Note]
> A rövid útmutató megkezdése előtt győződjön meg arról, hogy az Azure-előfizetése hozzáfér az Azure Spring Cloud-hoz.  Előzetes verziójú szolgáltatásként arra kérünk ügyfeleinket, hogy forduljon hozzánk, hogy fel tudja venni az előfizetését az engedélyezési listára.  Ha szeretné felfedezni az Azure Spring Cloud képességeit, lépjen kapcsolatba velünk e-mailben: azure-spring-cloud@service.microsoft.com.

>[!TIP]
> Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat.  A közös Azure-eszközök előre telepítve vannak, beleértve a git, a JDK, a Maven és az Azure CLI legújabb verzióit. Ha bejelentkezett az Azure-előfizetésbe, indítsa el a [Azure Cloud shellt](https://shell.azure.com) a shell.Azure.com webhelyről.  A Azure Cloud Shellről a [dokumentáció beolvasásával](../cloud-shell/overview.md) tájékozódhat bővebben

A gyorsútmutató elvégzéséhez:

1. [A Git telepítése](https://git-scm.com/)
2. [A JDK 8 telepítése](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [A Maven 3,0-es vagy újabb verziójának telepítése](https://maven.apache.org/download.cgi)
4. [Telepítse az Azure CLI-t](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Feliratkozás Azure-előfizetésre](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>Az Azure CLI-bővítmény telepítése

Telepítse az Azure CLI-hez készült Azure Spring Cloud-bővítményt az alábbi paranccsal

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Szolgáltatási példány kiépítése a Azure Portal

1. A böngészőben nyissa meg a [Azure Portal](https://portal.azure.com), és jelentkezzen be a fiókjába.

1. Keresse meg az **Azure Spring cloudt** , és válassza ki az Áttekintés oldalt. A kezdéshez kattintson a **Létrehozás** gombra.

1. Töltse ki az űrlapot, figyelembe véve a következő irányelveket:
    - Szolgáltatás neve: Adja meg a szolgáltatás példányának nevét.  A névnek 4 – 32 karakter hosszúnak kell lennie, és csak kisbetűket, számokat és kötőjeleket tartalmazhat.  A szolgáltatás nevének első karakterének betűnek kell lennie, és az utolsó karakternek betűnek vagy számnak kell lennie.
    - Előfizetés: Válassza ki azt az előfizetést, amelyet számlázni szeretne az erőforráshoz.  Győződjön meg arról, hogy ez az előfizetés hozzá lett adva az Azure Spring Cloud engedélyezési listájához.
    - Erőforráscsoport: Az új erőforrásokhoz új erőforráscsoportok létrehozása ajánlott eljárás.
    - Helyen Válassza ki a szolgáltatási példány helyét. A jelenleg támogatott helyszínek közé tartozik az USA keleti régiója, USA 2. nyugati régiója, Nyugat-Európa és Délkelet-Ázsia.
    
A szolgáltatás üzembe helyezése körülbelül 5 percet vesz igénybe.  A telepítés után megjelenik a szolgáltatási példány **Áttekintés** lapja.

## <a name="set-up-your-configuration-server"></a>A konfigurációs kiszolgáló beállítása

1. Lépjen a szolgáltatás **áttekintése** lapra, és válassza a **konfigurációs kiszolgáló**lehetőséget.
1. Az **alapértelmezett adattár** szakaszban állítsa az **URI** -t "https://github.com/Azure-Samples/piggymetrics" értékre, állítsa a **feliratot** a "config" értékre, majd kattintson az **alkalmaz** gombra a módosítások mentéséhez.

## <a name="clone-and-build-the-sample-application-repository"></a>A minta alkalmazás-adattár klónozása és összeállítása

1. A git-tárház klónozásához futtassa a következő parancsot.

    ```azurecli
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Váltson át a könyvtárra, és hozza létre a projektet a következő parancs futtatásával.

    ```azurecli
    cd PiggyMetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-and-deploy-the-azure-spring-cloud-configuration"></a>Az Azure Spring Cloud konfigurációjának előállítása és üzembe helyezése

1. Adja hozzá a következőt a `pom.xml` vagy a `settings.xml` értékhez, hogy a Maven működjön az Azure Spring Cloud használatával.

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

1. Állítson be egy konfigurációt a következő parancs futtatásával.

    ```azurecli
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:config
    ```

    1. Válassza ki a modulokat `gateway`, `auth-service` és `account-service`.

    1. Válassza ki az előfizetését és az Azure Spring Cloud Service-fürtöt.

    1. A megadott projektek listájában adja meg a `gateway` értéknek megfelelő számot a nyilvános hozzáférés biztosítása érdekében.
    
    1. Erősítse meg a konfigurációt.

1. Telepítse az alkalmazásokat a következő parancs használatával:

   ```azurecli
   mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:deploy
   ```

1. A PiggyMetrics az előző parancs kimenetében megadott URL-címen keresztül érheti el.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy Spring Cloud-alkalmazást telepített egy Maven-tárházból.  Ha többet szeretne megtudni az Azure Spring Cloud-ról, folytassa az alkalmazás üzembe helyezésének előkészítésével foglalkozó oktatóanyaggal.

> [!div class="nextstepaction"]
> [Az Azure Spring Cloud-alkalmazás előkészítése az üzembe helyezéshez](spring-cloud-tutorial-prepare-app-deployment.md)
