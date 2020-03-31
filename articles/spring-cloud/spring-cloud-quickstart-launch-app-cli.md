---
title: 'Rövid útmutató: Java Spring alkalmazás indítása az Azure CLI használatával'
description: Ebben a rövid útmutatóban üzembe helyez egy mintaalkalmazást az Azure Spring Cloud szolgáltatásban az Azure CLI-n.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 02/15/2020
ms.author: brendm
ms.openlocfilehash: c05e53bd8ad8ade8c1e42729f46c99a0059c4dce
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79470860"
---
# <a name="quickstart-launch-a-java-spring-application-using-the-azure-cli"></a>Rövid útmutató: Java Spring alkalmazás indítása az Azure CLI használatával

Az Azure Spring Cloud lehetővé teszi, hogy könnyedén futtasson egy tavaszi rendszerindításalapú mikroszolgáltatási alkalmazást az Azure-ban.

Ez a rövid útmutató bemutatja, hogyan telepíthet egy meglévő Java Spring Cloud-alkalmazást az Azure-ba. Ha elkészült, továbbra is kezelheti az alkalmazást az Azure CLI-n keresztül, vagy átválthat az Azure Portal használatával.

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

## <a name="provision-a-service-instance-on-the-azure-cli"></a>Szolgáltatáspéldány kiépítése az Azure CLI-n

1. Jelentkezzen be az Azure CLI-be, és válassza ki az aktív előfizetést. Ügyeljen arra, hogy válassza ki az Aktív előfizetés, amely az Azure Spring Cloud

    ```azurecli
        az login
        az account list -o table
        az account set --subscription <Name or ID of subscription from the last step>
    ```

2. Készítsen nevet az Azure Spring Cloud szolgáltatáshoz.  A név nek 4 és 32 karakter közötti nek kell lennie, és csak kisbetűket, számokat és kötőjeleket tartalmazhat.  A szolgáltatásnév első karakterének betűnek, az utolsó karakternek pedig betűnek vagy számnak kell lennie.

3. Hozzon létre egy erőforráscsoportot, amely tartalmazza az Azure Spring Cloud szolgáltatást.

    ```azurecli
        az group create --location eastus --name <resource group name>
    ```

    Itt tekinthet meg további információt az [Azure-erőforráscsoportokkal](../azure-resource-manager/management/overview.md) kapcsolatban.

4. Nyisson meg egy Azure CLI-ablakot, és futtassa a következő parancsokat az Azure Spring Cloud egy példányának kiépítéséhez.

    ```azurecli
        az spring-cloud create -n <service instance name> -g <resource group name>
    ```

    A szolgáltatáspéldány üzembe helyezése körülbelül öt percet vesz igénybe.

5. Állítsa be az alapértelmezett erőforráscsoport- és fürtnevet a következő parancsokkal:

    ```azurecli
        az configure --defaults group=<resource group name>
        az configure --defaults spring-cloud=<service instance name>
    ```

> [!div class="nextstepaction"]
> [Belefutottam egy problémába.](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=provision)

## <a name="setup-your-configuration-server"></a>A konfigurációs kiszolgáló beállítása

Frissítse a konfigurációs kiszolgálót a projektgit tárházának helyével:

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/piggymetrics-config
```

> [!div class="nextstepaction"]
> [Belefutottam egy problémába.](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=config-server)

## <a name="build-the-microservices-applications-locally"></a>A mikroszolgáltatások alkalmazásának helyi létrehozása

1. Hozzon létre egy új mappát, és klónozza a mintaalkalmazás-tárházat az Azure Cloud-fiókjába.  

    ```console
        mkdir source-code
        git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Módosítsa a könyvtárat, és építse fel a projektet.

    ```console
        cd piggymetrics
        mvn clean package -D skipTests
    ```

A projekt összeállítása körülbelül 5 percet vesz igénybe.  Miután elkészült, akkor meg kell az egyes JAR fájlokat minden szolgáltatás a megfelelő mappákat.

## <a name="create-the-microservices"></a>A mikroszolgáltatások létrehozása

Hozzon létre spring cloud mikroszolgáltatásokat az előző lépésben beépített JAR-fájlokkal. Három mikroszolgáltatást hoz létre: **átjárót**, **hitelesítési szolgáltatást**és **fiókszolgáltatást**.

```azurecli
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

## <a name="deploy-applications-and-set-environment-variables"></a>Alkalmazások telepítése és környezeti változók beállítása

Ténylegesen üzembe kell helyeznünk alkalmazásainkat az Azure-ba. Mindhárom alkalmazás központi telepítéséhez használja a következő parancsokat:

```azurecli
az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
```

> [!div class="nextstepaction"]
> [Belefutottam egy problémába.](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=deploy)

## <a name="assign-public-endpoint-to-gateway"></a>Nyilvános végpont hozzárendelése az átjáróhoz

Szükségünk van egy módja annak, hogy az alkalmazás elérése egy webböngészőn keresztül. Átjáróalkalmazásunknak nyilvános végpontra van szüksége.

1. Rendelje hozzá a végpontot a következő paranccsal:

```azurecli
az spring-cloud app update -n gateway --is-public true
```

2. Az **átjáróalkalmazás** lekérdezése a nyilvános IP-címhez, így ellenőrizheti, hogy fut-e az alkalmazás:

Linux:

```azurecli
az spring-cloud app show --name gateway | grep url
```

Windows:

```azurecli
az spring-cloud app show -s <service name> -g <resource group> -n gateway -o table
```

3. Keresse meg az előző parancs által biztosított URL-címet a PiggyMetrics alkalmazás futtatásához.
    ![Képernyőkép: A PiggyMetrics futása](media/spring-cloud-quickstart-launch-app-cli/launch-app.png)

Az Azure Portalon is megkeresheti az URL-címet. 
1. Navigálás a szolgáltatásra
2. **Alkalmazások** kiválasztása
3. **Átjáró** kiválasztása

    ![Képernyőkép: A PiggyMetrics futása](media/spring-cloud-quickstart-launch-app-cli/navigate-app1.png)
    
4. Az URL megkeresése az ![ **átjáró áttekintése** lapon Képernyőkép a PiggyMetrics futásáról](media/spring-cloud-quickstart-launch-app-cli/navigate-app2-url.png)

> [!div class="nextstepaction"]
> [Belefutottam egy problémába.](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban üzembe helyezett egy Spring Cloud-alkalmazást az Azure CLI-ből.  Ha többet szeretne megtudni az Azure Spring Cloudról, folytassa az alkalmazás üzembe helyezésre való előkészítéséről szóló oktatóanyaggal.

> [!div class="nextstepaction"]
> [Készítse elő az Azure Spring Cloud-alkalmazást a telepítéshez](spring-cloud-tutorial-prepare-app-deployment.md)

További minták érhetők el a GitHubon: [Azure Spring Cloud Samples.](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql)
