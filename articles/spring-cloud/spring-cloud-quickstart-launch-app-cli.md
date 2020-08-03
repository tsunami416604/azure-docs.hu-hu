---
title: 'Gyors útmutató: Java Spring-alkalmazás elindítása az Azure CLI használatával'
description: Ebben a rövid útmutatóban egy minta alkalmazást helyez üzembe az Azure Spring Cloud-on az Azure CLI-ben.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 02/15/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 53e27e4fafaf4468c9c2f4a1ce24ace3d317dada
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87503259"
---
# <a name="quickstart-launch-a-java-spring-application-using-the-azure-cli"></a>Gyors útmutató: Java Spring-alkalmazás elindítása az Azure CLI használatával

Az Azure Spring Cloud lehetővé teszi, hogy könnyedén futtasson egy Spring boot-alapú Services-alkalmazást az Azure-ban.

Ez a rövid útmutató bemutatja, hogyan helyezhet üzembe egy meglévő Java Spring Cloud-alkalmazást az Azure-ban. Ha elkészült, továbbra is kezelheti az alkalmazást az Azure CLI-n keresztül, vagy átválthat a Azure Portal használatára.

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

```azurecli
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-on-the-azure-cli"></a>Szolgáltatási példány kiépítése az Azure CLI-ben

1. Jelentkezzen be az Azure CLI-be, és válassza ki az aktív előfizetését. Ügyeljen arra, hogy az Azure Spring Cloud számára engedélyezett aktív előfizetést válassza

    ```azurecli
        az login
        az account list -o table
        az account set --subscription <Name or ID of subscription from the last step>
    ```

2. Készítse elő az Azure Spring Cloud-szolgáltatás nevét.  A névnek 4 – 32 karakter hosszúnak kell lennie, és csak kisbetűket, számokat és kötőjeleket tartalmazhat.  A szolgáltatás nevének első karakterének betűnek kell lennie, és az utolsó karakternek betűnek vagy számnak kell lennie.

3. Hozzon létre egy erőforráscsoportot, amely tartalmazza az Azure Spring Cloud Service-t.

    ```azurecli
        az group create --location eastus --name <resource group name>
    ```

    Itt tekinthet meg további információt az [Azure-erőforráscsoportokkal](../azure-resource-manager/management/overview.md) kapcsolatban.

4. Nyisson meg egy Azure CLI-ablakot, és futtassa az alábbi parancsokat az Azure Spring Cloud egy példányának kiépítéséhez.

    ```azurecli
        az spring-cloud create -n <service instance name> -g <resource group name>
    ```

    A szolgáltatás üzembe helyezése körülbelül öt percet vesz igénybe.

5. Állítsa be az alapértelmezett erőforráscsoport nevét és a fürt nevét az alábbi parancsokkal:

    ```azurecli
        az configure --defaults group=<resource group name>
        az configure --defaults spring-cloud=<service instance name>
    ```

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=provision)

## <a name="setup-your-configuration-server"></a>A konfigurációs kiszolgáló beállítása

Frissítse a config-Servert a projekt git-tárházának a helyére:

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/piggymetrics-config
```

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=config-server)

## <a name="build-the-microservices-applications-locally"></a>A Service-alkalmazások helyi létrehozása

1. Hozzon létre egy új mappát, és a minta alkalmazás-tárházat az Azure Cloud-fiókjába.  

    ```console
        mkdir source-code
        git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Módosítsa a könyvtárat, és hozza létre a projektet.

    ```console
        cd piggymetrics
        mvn clean package -D skipTests
    ```

A projekt fordítása körülbelül 5 percet vesz igénybe.  Ha elkészült, egyedi JAR-fájlokkal kell rendelkeznie az egyes szolgáltatásokhoz a megfelelő mappákban.

## <a name="create-the-microservices"></a>A Service-szolgáltatások létrehozása

Hozzon létre Spring Cloud-szolgáltatásokat az előző lépésben létrehozott JAR-fájlok használatával. Három szolgáltatást fog létrehozni: **átjáró**, **Auth-szolgáltatás**és **szolgáltatásfiók**.

```azurecli
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

## <a name="deploy-applications-and-set-environment-variables"></a>Alkalmazások központi telepítése és környezeti változók beállítása

Az alkalmazásokat ténylegesen üzembe kell helyezni az Azure-ban. A következő parancsokkal telepítheti mindhárom alkalmazást:

```azurecli
az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
```

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=deploy)

## <a name="assign-public-endpoint-to-gateway"></a>Nyilvános végpont kiosztása az átjáróhoz

Egy webböngészőn keresztül elérhetővé kell tennie az alkalmazást. Az átjáró alkalmazásnak nyilvános végpontra van szüksége.

1. Rendelje hozzá a végpontot a következő parancs használatával:

```azurecli
az spring-cloud app update -n gateway --is-public true
```

2. A nyilvános IP-címhez tartozó **átjáró** alkalmazás lekérdezése, hogy ellenőrizze, hogy fut-e az alkalmazás:

```azurecli
az spring-cloud app show --name gateway --query properties.url
```

3. Navigáljon az előző parancs által megadott URL-címhez a PiggyMetrics alkalmazás futtatásához.
    ![A PiggyMetrics-t futtató képernyőkép](media/spring-cloud-quickstart-launch-app-cli/launch-app.png)

Az URL-cím megkereséséhez navigáljon a Azure Portal is. 
1. Navigáljon a szolgáltatáshoz
2. **Alkalmazások** kiválasztása
3. **Átjáró** kiválasztása

    ![A PiggyMetrics-t futtató képernyőkép](media/spring-cloud-quickstart-launch-app-cli/navigate-app1.png)
    
4. Az URL-cím megkeresése az **átjáró áttekintő** lapján a PiggyMetrics-t ![ futtató képernyőképen](media/spring-cloud-quickstart-launch-app-cli/navigate-app2-url.png)

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy Spring Cloud-alkalmazást helyezett üzembe az Azure CLI-vel.  Ha többet szeretne megtudni az Azure Spring Cloud-ról, folytassa az alkalmazás üzembe helyezésének előkészítésével foglalkozó oktatóanyaggal.

> [!div class="nextstepaction"]
> [Az Azure Spring Cloud-alkalmazás előkészítése az üzembe helyezéshez](spring-cloud-tutorial-prepare-app-deployment.md)

További minták érhetők el a GitHubon: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
