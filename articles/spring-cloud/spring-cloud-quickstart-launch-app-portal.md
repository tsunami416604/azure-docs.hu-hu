---
title: Azure Spring Cloud-alkalmazás elindítása a Azure Portal használatával
description: A Azure Portal használatával üzembe helyezhet egy minta-alkalmazást az Azure Spring-felhőben.
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/04/2019
ms.author: v-vasuke
ms.openlocfilehash: 11e9fadc240b90ceb7e4a0e6690978dac9bae859
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255076"
---
# <a name="quickstart-launch-an-azure-spring-cloud-application-using-the-azure-portal"></a>Gyors útmutató: Azure Spring Cloud-alkalmazás elindítása a Azure Portal használatával

Az Azure Spring Cloud lehetővé teszi, hogy könnyedén futtasson Spring Cloud-alapú Service-alkalmazásokat az Azure-ban.

Ez a rövid útmutató bemutatja, hogyan helyezhet üzembe egy meglévő Spring Cloud-alkalmazást az Azure-ban. [Itt látható](https://github.com/Azure-Samples/PiggyMetrics) az oktatóanyagban használt minta alkalmazás kódjára mutató hivatkozás. Ha elkészült, a megadott minta alkalmazás elérhető lesz az interneten, és készen áll a Azure Portalon keresztül történő felügyeletre.

Ez a rövid útmutató a következőket ismerteti:

> [!div class="checklist"]
> * Szolgáltatási példány kiépítése
> * Konfigurációs kiszolgáló beállítása egy példányhoz
> * A Services-alkalmazások helyi létrehozása
> * Az egyes szolgáltatások üzembe helyezése
> * Nyilvános végpont kiosztása az alkalmazáshoz

## <a name="prerequisites"></a>Előfeltételek

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

1. A böngészőben nyissa meg az [Azure Spring Cloud-ra mutató hivatkozást a Azure Portal](https://ms.portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=AppPlatformExtension#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/home/searchQuery/Azure%20Spring%20Cloud).

1. Válassza az **Azure Spring Cloud** lehetőséget az Áttekintés oldalra való ugráshoz. Ezután kattintson a **Létrehozás** gombra a kezdéshez.

1. Töltse ki az űrlapot, figyelembe véve a következő irányelveket:
    - Szolgáltatás neve: adja meg a szolgáltatás példányának nevét.  A névnek 4 – 32 karakter hosszúnak kell lennie, és csak kisbetűket, számokat és kötőjeleket tartalmazhat.  A szolgáltatás nevének első karakterének betűnek kell lennie, és az utolsó karakternek betűnek vagy számnak kell lennie.
    - Előfizetés: válassza ki azt az előfizetést, amelyet számlázni szeretne az erőforráshoz.  Győződjön meg arról, hogy ez az előfizetés hozzá lett adva az Azure Spring Cloud engedélyezési listájához.
    - Erőforráscsoport: új erőforráscsoportok létrehozása új erőforrásokhoz az ajánlott eljárás.
    - Hely: válassza ki a szolgáltatás példányának helyét. A jelenleg támogatott helyszínek közé tartozik az USA keleti régiója, USA 2. nyugati régiója, Nyugat-Európa és Délkelet-Ázsia.
    
A szolgáltatás üzembe helyezése körülbelül 5 percet vesz igénybe.  A telepítés után megjelenik a szolgáltatási példány **Áttekintés** lapja.

## <a name="set-up-your-configuration-server"></a>A konfigurációs kiszolgáló beállítása

1. Lépjen a szolgáltatás **áttekintése** lapra, és válassza a **konfigurációs kiszolgáló**lehetőséget.

1. Az **alapértelmezett adattár** szakaszban állítsa az **URI** -t "https://github.com/Azure-Samples/piggymetrics" értékre, állítsa a **feliratot** a "config" értékre, majd kattintson az **alkalmaz** gombra a módosítások mentéséhez.

## <a name="build-and-deploy-microservice-applications"></a>Service-alkalmazások készítése és üzembe helyezése

1. Nyisson meg egy parancssorablakot, és futtassa a következő parancsot a minta alkalmazás tárházának a helyi gépre történő klónozásához.

    ```cli
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

1. Hozza létre a projektet az alábbi parancs futtatásával.

    ```cli
    cd PiggyMetrics
    mvn clean package -DskipTests
    ```

1. Jelentkezzen be az Azure CLI-be, és állítsa be az aktív előfizetését.

    ```cli
    # Login to Azure CLI
    az login

    # List all subscriptions
    az account list -o table

    # Set active subscription
    az account set --subscription <target subscription ID>
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

1. Ugyanezt a mintát követve hozza létre a `account-service` és a `auth-service` alkalmazást, és telepítse a JAR-fájljait.

    ```cli
    az spring-cloud app create -n account-service
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app create -n auth-service
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

1. Az alkalmazások üzembe helyezésének befejezése néhány percet vesz igénybe. Annak ellenőrzéséhez, hogy telepítve vannak-e, nyissa meg az **alkalmazások** panelt a Azure Portal. A három alkalmazás sorát kell látnia.

## <a name="assign-a-public-endpoint-to-gateway"></a>Nyilvános végpont társítása az átjáróhoz

1. Nyissa meg az **alkalmazás-irányítópult** lapot.
2. Válassza ki a `gateway` alkalmazást az **alkalmazás részletei** lap megjelenítéséhez.
3. Válassza a **tartomány kiosztása** lehetőséget egy nyilvános végpontnak az átjáróhoz való hozzárendeléséhez. Ez néhány percet is igénybe vehet. 
4. A futó alkalmazás megtekintéséhez adja meg a hozzárendelt nyilvános IP-címet a böngészőben.


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
