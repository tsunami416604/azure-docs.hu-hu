---
title: Rövid útmutató – Spring Cloud-alkalmazás elindítása forráskódból
description: Ebből a rövid útmutatóból megtudhatja, hogyan indíthatja el az Azure Spring Cloud-alkalmazást közvetlenül a forráskódból
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/30/2019
ms.author: brendm
ms.openlocfilehash: 3ab4b1729ea380671b72a9bb01740930a186d5c3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "79470794"
---
# <a name="quickstart-launch-your-spring-cloud-application-from-source-code"></a>Rövid útmutató: a Spring Cloud-alkalmazás elindítása forráskódból

Az Azure Spring Cloud lehetővé teszi, hogy könnyedén futtasson Spring Cloud-alapú Service-alkalmazásokat az Azure-ban.

Az Azure Spring Cloud lehetővé teszi, hogy az alkalmazást közvetlenül a Java-forráskódból vagy egy előre elkészített JAR-ból indítsa el. Ez a cikk végigvezeti a szükséges lépéseken.

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

Mielőtt elkezdené, győződjön meg arról, hogy az Azure-előfizetése rendelkezik a szükséges függőségekkel:

1. [A Git telepítése](https://git-scm.com/)
2. [A JDK 8 telepítése](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
3. [A Maven 3,0-es vagy újabb verziójának telepítése](https://maven.apache.org/download.cgi)
4. [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Feliratkozás Azure-előfizetésre](https://azure.microsoft.com/free/)

> [!TIP]
> Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat.  A közös Azure-eszközök előre telepítve vannak, beleértve a git, a JDK, a Maven és az Azure CLI legújabb verzióit. Ha bejelentkezett az Azure-előfizetésbe, indítsa el a [Azure Cloud shellt](https://shell.azure.com) a shell.Azure.com webhelyről.  A Azure Cloud Shellről a [dokumentáció beolvasásával](../cloud-shell/overview.md) tájékozódhat bővebben

## <a name="install-the-azure-cli-extension"></a>Az Azure CLI-bővítmény telepítése

Telepítse az Azure CLI-bővítményt az Azure CLI-hez a következő paranccsal

```azurecli
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>Szolgáltatási példány kiépítése az Azure CLI használatával

Jelentkezzen be az Azure CLI-be, és válassza ki az aktív előfizetését. Ügyeljen arra, hogy az Azure Spring Cloud számára engedélyezett aktív előfizetést válassza

```azurecli
az login
az account list -o table
az account set --subscription
```

Hozzon létre egy erőforráscsoportot, amely tartalmazza az Azure Spring Cloud Service-t. További információ az Azure- [erőforráscsoportok](../azure-resource-manager/management/overview.md).

```azurecli
az group create --location eastus --name <resource group name>
```

Futtassa az alábbi parancsokat az Azure Spring Cloud egy példányának kiépítéséhez. Készítse elő az Azure Spring Cloud-szolgáltatás nevét. A névnek 4 – 32 karakter hosszúnak kell lennie, és csak kisbetűket, számokat és kötőjeleket tartalmazhat. A szolgáltatás nevének első karakterének betűnek kell lennie, és az utolsó karakternek betűnek vagy számnak kell lennie.

```azurecli
az spring-cloud create -n <resource name> -g <resource group name>
```

A szolgáltatási példány körülbelül öt percet vesz igénybe.

Állítsa be az alapértelmezett erőforráscsoport nevét és a fürt nevét az alábbi parancsokkal:

```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=provision)

## <a name="create-the-spring-cloud-application"></a>A Spring Cloud-alkalmazás létrehozása

A következő parancs egy Spring Cloud-alkalmazást hoz létre az előfizetésében.  Ezzel létrehoz egy üres Spring Cloud Service-szolgáltatást, amellyel feltöltheti az alkalmazást.

```azurecli
az spring-cloud app create -n <app-name>
```

## <a name="deploy-your-spring-cloud-application"></a>A Spring Cloud-alkalmazás üzembe helyezése

Az alkalmazást üzembe helyezheti egy előre elkészített JAR vagy egy Gradle vagy Maven adattárból.  Az alábbi esetekben talál útmutatást.

### <a name="deploy-a-built-jar"></a>Beépített JAR üzembe helyezése

A helyi gépre épülő JAR üzembe helyezéséhez győződjön meg arról, hogy a Build [FAT-jar-](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-build.html#howto-create-an-executable-jar-with-maven)t hoz létre.

A FAT-JAR telepítése aktív központi telepítésre

```azurecli
az spring-cloud app deploy -n <app-name> --jar-path <path-to-fat-JAR>
```

A FAT-JAR üzembe helyezése egy adott központi telepítésben

```azurecli
az spring-cloud app deployment create --app <app-name> -n <deployment-name> --jar-path <path-to-built-jar>
```

### <a name="deploy-from-source-code"></a>Üzembe helyezés forráskódból

Az Azure Spring Cloud a [kpack](https://github.com/pivotal/kpack) használatával hozza létre a projektet.  Az Azure CLI-vel feltöltheti a forráskódot, felépítheti a projektet a kpack használatával, és üzembe helyezheti azt a célalkalmazás alkalmazásban.

> [!WARNING]
> A projektnek csak egy `main-class` jar-fájlt `MANIFEST.MF` kell létrehoznia `target` (a Maven üzembe helyezéséhez vagy `build/libs` a (Gradle-telepítésekhez).  Több JAR-fájl `main-class` bejegyzéseivel a telepítés sikertelen lesz.

Egymodulos Maven/Gradle-projektekhez:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name>
```

Több modult tartalmazó Maven/Gradle-projektek esetében ismételje meg az egyes modulokat:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name> --target-module <relative-path-to-module>
```

### <a name="show-deployment-logs"></a>Telepítési naplók megjelenítése

Tekintse át a kpack-létrehozási naplókat a következő paranccsal:

```azurecli
az spring-cloud app show-deploy-log -n <app-name> [-d <deployment-name>]
```

> [!NOTE]
> A kpack-naplók csak akkor jelennek meg a legújabb központi telepítésben, ha a központi telepítés forrása a kpack használatával készült.

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=deploy)

## <a name="assign-a-public-endpoint-to-gateway"></a>Nyilvános végpont társítása az átjáróhoz

1. Nyissa meg az **alkalmazás-irányítópult** lapot.
2. Válassza ki `gateway` az alkalmazást az **alkalmazás részletei** lap megjelenítéséhez.
3. Válassza a **tartomány kiosztása** lehetőséget egy nyilvános végpontnak az átjáróhoz való hozzárendeléséhez. Ez néhány percet is igénybe vehet. 
4. A futó alkalmazás megtekintéséhez adja meg a hozzárendelt nyilvános IP-címet a böngészőben.

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=public-endpoint)

## <a name="next-steps"></a>További lépések

Ennek a rövid útmutatónak a segítségével megtanulta a következőket:

> [!div class="checklist"]
> * Szolgáltatási példány kiépítése
> * Konfigurációs kiszolgáló beállítása egy példányhoz
> * A Services-alkalmazások helyi létrehozása
> * Az egyes szolgáltatások üzembe helyezése
> * Alkalmazások környezeti változóinak szerkesztése
> * Nyilvános IP-cím kiosztása az Application gatewayhez

> [!div class="nextstepaction"]
> [Az Azure Spring Cloud-alkalmazás előkészítése az üzembe helyezéshez](spring-cloud-tutorial-prepare-app-deployment.md)

További minták érhetők el a GitHubon: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
