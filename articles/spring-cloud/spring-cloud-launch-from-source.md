---
title: Rövid útmutató - Indítsa el tavaszi felhőalkalmazását forráskódból
description: Ebben a rövid útmutatóban megtudhatja, hogyan indíthatja el közvetlenül az Azure Spring Cloud-alkalmazást a forráskódból
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/30/2019
ms.author: brendm
ms.openlocfilehash: 3ab4b1729ea380671b72a9bb01740930a186d5c3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79470794"
---
# <a name="quickstart-launch-your-spring-cloud-application-from-source-code"></a>Rövid útmutató: Indítsa el a Spring Cloud alkalmazást forráskódból

Az Azure Spring Cloud lehetővé teszi a Spring Cloud alapú mikroszolgáltatási alkalmazások egyszerű futtatását az Azure-ban.

Az Azure Spring Cloud lehetővé teszi, hogy közvetlenül a java forráskódból vagy egy előre elkészített JAR-ból indítsa el az alkalmazást. Ez a cikk végigvezeti a szükséges lépéseken.

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

Mielőtt elkezdené, győződjön meg arról, hogy az Azure-előfizetés rendelkezik a szükséges függőségek:

1. [A Git telepítése](https://git-scm.com/)
2. [A JDK 8 telepítése](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
3. [A Maven 3.0 vagy újabb telepítése](https://maven.apache.org/download.cgi)
4. [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Regisztráció Azure-előfizetésre](https://azure.microsoft.com/free/)

> [!TIP]
> Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat.  Előtelepített azure-eszközökkel rendelkezik, beleértve a Git, a JDK, a Maven és az Azure CLI legújabb verzióit. Ha be van jelentkezve az Azure-előfizetésbe, indítsa el az [Azure Cloud Shell-t](https://shell.azure.com) shell.azure.com.  Az Azure Cloud Shellről a [dokumentáció elolvasásával](../cloud-shell/overview.md) tudhat meg többet.

## <a name="install-the-azure-cli-extension"></a>Az Azure CLI bővítmény telepítése

Telepítse az Azure Spring Cloud bővítményt az Azure CLI-hez a következő paranccsal

```azurecli
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>Szolgáltatáspéldány kiépítése az Azure CLI használatával

Jelentkezzen be az Azure CLI-be, és válassza ki az aktív előfizetést. Ügyeljen arra, hogy válassza ki az Aktív előfizetés, amely az Azure Spring Cloud

```azurecli
az login
az account list -o table
az account set --subscription
```

Hozzon létre egy erőforráscsoportot, amely tartalmazza az Azure Spring Cloud szolgáltatást. További információ az [Azure Resource Groups](../azure-resource-manager/management/overview.md)szolgáltatásról.

```azurecli
az group create --location eastus --name <resource group name>
```

Futtassa a következő parancsokat az Azure Spring Cloud egy példányának kiépítéséhez. Készítsen nevet az Azure Spring Cloud szolgáltatáshoz. A név nek 4 és 32 karakter közötti nek kell lennie, és csak kisbetűket, számokat és kötőjeleket tartalmazhat. A szolgáltatásnév első karakterének betűnek, az utolsó karakternek pedig betűnek vagy számnak kell lennie.

```azurecli
az spring-cloud create -n <resource name> -g <resource group name>
```

A szolgáltatáspéldány üzembe helyezése körülbelül öt percet vesz igénybe.

Állítsa be az alapértelmezett erőforráscsoport- és fürtnevet a következő parancsokkal:

```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```

> [!div class="nextstepaction"]
> [Belefutottam egy problémába.](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=provision)

## <a name="create-the-spring-cloud-application"></a>A Spring Cloud alkalmazás létrehozása

A következő parancs létrehoz egy Spring Cloud-alkalmazást az előfizetésében.  Ez létrehoz egy üres Spring Cloud szolgáltatást, amelyre feltölthetjük az alkalmazásunkat.

```azurecli
az spring-cloud app create -n <app-name>
```

## <a name="deploy-your-spring-cloud-application"></a>A Spring Cloud alkalmazás üzembe helyezése

Az alkalmazás üzembe helyezheti egy előre elkészített JAR vagy egy Gradle vagy Maven tárház.  Az alábbi használati útmutatót az egyes esetekkel kapcsolatban találja.

### <a name="deploy-a-built-jar"></a>Beépített JAR telepítése

A helyi számítógépre épített JAR-ból történő üzembe helyezéshez győződjön meg arról, hogy a build [zsír-JAR-t hoz](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-build.html#howto-create-an-executable-jar-with-maven)létre.

A fat-JAR telepítése egy aktív üzembe helyezéshez

```azurecli
az spring-cloud app deploy -n <app-name> --jar-path <path-to-fat-JAR>
```

A zsír-JAR telepítése egy adott üzembe helyezéshez

```azurecli
az spring-cloud app deployment create --app <app-name> -n <deployment-name> --jar-path <path-to-built-jar>
```

### <a name="deploy-from-source-code"></a>Telepítés forráskódból

Az Azure Spring Cloud [kpack](https://github.com/pivotal/kpack) használatával készíti el a projektet.  Az Azure CLI segítségével feltöltheti a forráskódot, létrehozhatja a projektet a kpack használatával, és üzembe helyezheti azt a célalkalmazásba.

> [!WARNING]
> A projektnek csak egy JAR-fájlt kell `main-class` létrehoznia, amelynek bejegyzése a `MANIFEST.MF` betáplálásban `target` van (Maven-telepítések esetén vagy `build/libs` (Gradle-telepítésekhez).  Több JAR-fájl bejegyzéssel `main-class` a központi telepítés sikertelensíti a telepítést.

Egymodulos Maven / Gradle projektek esetén:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name>
```

Több modullal rendelkező Maven / Gradle projektek esetén ismételje meg az egyes modulok esetében a műveletet:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name> --target-module <relative-path-to-module>
```

### <a name="show-deployment-logs"></a>Telepítési naplók megjelenítése

Tekintse át a kpack buildnaplókat a következő paranccsal:

```azurecli
az spring-cloud app show-deploy-log -n <app-name> [-d <deployment-name>]
```

> [!NOTE]
> A kpack naplók csak akkor jeleníti meg a legújabb központi telepítést, ha a központi telepítés forrásból készült a kpack használatával.

> [!div class="nextstepaction"]
> [Belefutottam egy problémába.](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=deploy)

## <a name="assign-a-public-endpoint-to-gateway"></a>Nyilvános végpont hozzárendelése az átjáróhoz

1. Nyissa meg az **Alkalmazás irányítópultja** lapot.
2. Válassza `gateway` ki az alkalmazást az Alkalmazás részletei lap **megjelenítéséhez.**
3. Válassza **a Tartomány hozzárendelése lehetőséget,** ha nyilvános végpontot szeretne hozzárendelni az átjáróhoz. Ez lehet pár perc. 
4. Adja meg a hozzárendelt nyilvános IP-címet a böngészőben a futó alkalmazás megtekintéséhez.

> [!div class="nextstepaction"]
> [Belefutottam egy problémába.](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=public-endpoint)

## <a name="next-steps"></a>További lépések

Ennek a rövid útmutatónak a segítségével megtanulta a következőket:

> [!div class="checklist"]
> * Szolgáltatáspéldány kiépítése
> * Példány konfigurációs kiszolgálójának beállítása
> * Mikroszolgáltatási alkalmazás létrehozása helyileg
> * Az egyes mikroszolgáltatások üzembe helyezése
> * Alkalmazások környezeti változóinak szerkesztése
> * Nyilvános IP-cím hozzárendelése az alkalmazásátjáróhoz

> [!div class="nextstepaction"]
> [Készítse elő az Azure Spring Cloud-alkalmazást a telepítéshez](spring-cloud-tutorial-prepare-app-deployment.md)

További minták érhetők el a GitHubon: [Azure Spring Cloud Samples.](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql)
