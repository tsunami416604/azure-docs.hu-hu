---
title: Gyors útmutató – alkalmazások létrehozása és üzembe helyezése az Azure Spring Cloud-ban
description: Az Azure Spring Cloud alkalmazás üzembe helyezését ismerteti.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: 770dd021a09e3ba0b1c2c6742ded3a73424b042f
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951749"
---
# <a name="quickstart-build-and-deploy-apps-to-azure-spring-cloud"></a>Gyors útmutató: alkalmazások létrehozása és üzembe helyezése az Azure Spring Cloud-ban

Ebből a dokumentumból megtudhatja, hogyan hozhat létre és helyezhet üzembe Service-alkalmazásokat az Azure Spring Cloud használatával:
* Azure CLI
* Maven beépülő modul
* IntelliJ

Az Azure CLI vagy Maven használatával történő üzembe helyezés előtt végezze [el az Azure Spring Cloud példányát kiépítő](spring-cloud-quickstart-provision-service-instance.md) példákat, és [állítsa be a konfigurációs kiszolgálót](spring-cloud-quickstart-setup-config-server.md).

## <a name="prerequisites"></a>Előfeltételek

* [A JDK 8 telepítése](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Feliratkozás Azure-előfizetésre](https://azure.microsoft.com/free/)
* Választható [Telepítse az Azure CLI 2.0.67 vagy újabb verzióját](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) , és telepítse az Azure Spring Cloud bővítményt a paranccsal: `az extension add --name spring-cloud`
* Választható [A Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) és a [Bejelentkezés](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in) telepítése

## <a name="deployment-procedures"></a>Üzembe helyezési eljárások

#### <a name="cli"></a>[Parancssori felület](#tab/Azure-CLI)

### <a name="build-the-microservices-applications-locally"></a>A Service-alkalmazások helyi létrehozása

1. A minta alkalmazás adattárának klónozása az Azure Cloud-fiókba.  

    ```azurecli
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Módosítsa a könyvtárat, és hozza létre a projektet.

    ```azurecli
    cd piggymetrics
    mvn clean package -DskipTests
    ```

A projekt fordítása körülbelül 5 percet vesz igénybe. Ha elkészült, egyedi JAR-fájlokkal kell rendelkeznie az egyes szolgáltatásokhoz a megfelelő mappákban.

### <a name="create-and-deploy-the-apps"></a>Alkalmazások létrehozása és központi telepítése

1. Állítsa be az alapértelmezett erőforráscsoport nevét és a fürt nevét az alábbi parancsokkal:

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

1. Hozzon létre Azure Spring Cloud-szolgáltatásokat az előző lépésben létrehozott JAR-fájlokkal. Három alkalmazást fog létrehozni: **átjáró**, **Auth-szolgáltatás**és **fiók-szolgáltatás**.

    ```azurecli
    az spring-cloud app create --name gateway
    az spring-cloud app create --name auth-service
    az spring-cloud app create --name account-service
    ```

1. Az alkalmazásokat ténylegesen üzembe kell helyezni az Azure-ban. A következő parancsokkal telepítheti mindhárom alkalmazást:

    ```azurecli
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

### <a name="assign-public-endpoint-to-gateway"></a>Nyilvános végpont kiosztása az átjáróhoz

Egy webböngészőn keresztül elérhetővé kell tennie az alkalmazást. Az átjáró alkalmazásnak nyilvános végpontra van szüksége.

1. Rendelje hozzá a végpontot a következő parancs használatával:

    ```azurecli
    az spring-cloud app update -n gateway --is-public true
    ```

2. A nyilvános IP-címhez tartozó **átjáró** alkalmazás lekérdezése, hogy ellenőrizze, hogy fut-e az alkalmazás:

    ```azurecli
    az spring-cloud app show --name gateway --query properties.url
    ```

#### <a name="maven"></a>[Maven](#tab/Maven)

### <a name="clone-and-build-the-sample-application-repository"></a>A minta alkalmazás-adattár klónozása és összeállítása

1. A git-tárház klónozásához futtassa a következő parancsot:

    ```
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Módosítsa a könyvtárat, és hozza létre a projektet a következő parancs futtatásával:

    ```
    cd piggymetrics
    mvn clean package -DskipTests
    ```

### <a name="generate-configurations-and-deploy-to-the-azure-spring-cloud"></a>Konfigurációk készítése és üzembe helyezése az Azure Spring Cloud-ban

1. Konfigurációk előállításához futtassa a következő parancsot a szülő POM-t tartalmazó PiggyMetrics gyökérkönyvtárában. Ha már bejelentkezett az Azure CLI-vel, akkor a parancs automatikusan felveszi a hitelesítő adatokat. Ellenkező esetben a rendszer útmutatást ad a bejelentkezéshez. További részletekért tekintse meg a [wiki oldalát](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication) .

    ```
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:1.1.0:config
    ```
    
    A rendszer a következőket kéri:
    * **Modulok:** Select `gateway` , `auth-service` , és ' Account-Service ' ')
    * **Előfizetés:** az Azure Spring Cloud-példánnyal létrehozott előfizetése
    * **Szolgáltatási példány:** a létrehozott Azure Spring Cloud-példány neve
    * **Nyilvános végpont:** A megadott projektek listájában adja meg azt a számot, amely a következőnek felel meg: `gateway` .

1. A POM mostantól tartalmazza a beépülő modul függőségeit és konfigurációit. Telepítse az alkalmazásokat a következő parancs használatával. 

    ```
    mvn azure-spring-cloud:deploy
    ```

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

### <a name="import-sample-project-in-intellij"></a>Mintavételezési projekt importálása a IntelliJ-ben

1. Töltse le és csomagolja ki az oktatóanyag forrás-tárházát, vagy klónozott a git használatával: `git clone https://github.com/Azure-Samples/piggymetrics` 

1. Nyissa meg az IntelliJ **üdvözlő** párbeszédpanelt, és válassza a **Projekt importálása** elemet az Importálás varázsló megnyitásához.

1. Válassza a `piggymetric` mappa lehetőséget.

    ![Projekt importálása](media/spring-cloud-intellij-howto/revision-import-project-1.png)

### <a name="deploy-gateway-app-to-azure-spring-cloud"></a>Gateway-alkalmazás üzembe helyezése az Azure Spring Cloud-on
Az Azure-ba való üzembe helyezéshez be kell jelentkeznie az Azure-fiókjával Azure Toolkit for IntelliJval, és ki kell választania az előfizetését. A bejelentkezés részleteiért lásd: [telepítés és bejelentkezés](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

1. Kattintson a jobb gombbal a projektre a IntelliJ Project Explorerben, és válassza az **Azure**-  ->  **üzembe helyezés az Azure Spring Cloud**-ban lehetőséget.

    ![Üzembe helyezés az Azure 1-ben](media/spring-cloud-intellij-howto/revision-deploy-to-azure-1.png)

1. A **név** mezőben Hozzáfűzés: a meglévő **név** *átjárója* a konfigurációra hivatkozik.
1. Az összetevő **szövegmezőben** válassza a *com. piggymetrics: Gateway: 1.0-pillanatkép*elemet.
1. Az **előfizetés** szövegmezőben ellenőrizze az előfizetését.
1. A **Spring Cloud** szövegmezőben válassza ki azt az Azure Spring Cloud-példányt, amelyet az [Azure Spring Cloud-példány kiépítése](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-provision-service-instance)című részben hozott létre.
1. Az *engedélyezéshez*állítsa be a **nyilvános végpontot** .
1. Az **alkalmazás:** szövegmezőben válassza az **alkalmazás létrehozása...** lehetőséget.
1. Adja meg az *átjárót*, majd kattintson **az OK**gombra.

    ![Üzembe helyezés az Azure-ban OK](media/spring-cloud-intellij-howto/revision-deploy-to-azure-2.png)

1. A párbeszédpanel **Indítás előtti** szakaszában kattintson duplán a * Maven-cél futtatása * * elemre.
1. A **Working Directory** szövegmezőben navigáljon a *piggymetrics/Gateway* mappára.
1. A **parancssori** szövegmezőbe írja be a *Package-DskipTests*értéket. Kattintson az **OK** gombra.
1. A telepítés elindításához kattintson a **Futtatás** gombra az **Azure Spring Cloud app üzembe helyezése** párbeszédpanel alján. A beépülő modul futtatja a parancsot `mvn package` az `gateway` alkalmazáson, és telepíti a parancs által generált jar-t `package` .

### <a name="deploy-auth-service-and-account-service-apps-to-azure-spring-cloud"></a>Auth-szolgáltatás és fiók-szolgáltatásbeli alkalmazások üzembe helyezése az Azure Spring Cloud-ban
A fenti lépéseket megismételve üzembe helyezheti és telepítheti `auth-service` az `account-service` alkalmazásokat az Azure Spring Cloud szolgáltatásban. biztosra menni:

1. Módosítsa a **nevet** és **az összetevőt az** alkalmazás azonosításához `auth-service` .
1. Az **alkalmazás:** szövegmezőben válassza az **alkalmazás létrehozása...** lehetőséget az alkalmazások létrehozásához. `auth-service`
1. Győződjön meg arról, hogy a **nyilvános végpont** beállítás *Letiltva*értékre van állítva.
1. A párbeszédpanel **Indítás előtt** szakaszában váltson a **munkakönyvtárra** a *piggymetrics/Auth-Service* mappára.
1. A telepítés elindításához kattintson a **Futtatás** gombra az **Azure Spring Cloud app üzembe helyezése** párbeszédpanel alján. 
1. Ismételje meg ezeket az eljárásokat a konfigurálásához és telepítéséhez `account-service` .
---

A PiggyMetrics alkalmazás eléréséhez navigáljon az előző lépések kimenetében megadott URL-címhez. emelkedés pl `https://<service instance name>-gateway.azuremicroservices.io`

![PiggyMetrics elérése](media/spring-cloud-quickstart-launch-app-cli/launch-app.png)

Az URL-cím megkereséséhez navigáljon a Azure Portal is. 
1. Navigáljon a szolgáltatáshoz
2. **Alkalmazások** kiválasztása
3. **Átjáró** kiválasztása

    ![Alkalmazás navigálása](media/spring-cloud-quickstart-launch-app-cli/navigate-app1.png)
    
4. Az URL-cím megkeresése az **átjárón | Áttekintő** lap

    ![Alkalmazás második navigálása](media/spring-cloud-quickstart-launch-app-cli/navigate-app2-url.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Az előző lépésekben Azure-erőforrásokat hozott létre egy erőforráscsoportban. Ha nem várható, hogy a jövőben szüksége lenne ezekre az erőforrásokra, törölje az erőforráscsoportot a portálról, vagy futtassa a következő parancsot a Cloud Shellban:
```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```
Az előző lépésekben az erőforráscsoport alapértelmezett nevét is megadhatja. Az alapértelmezett beállítás törléséhez futtassa a következő parancsot a Cloud Shellban:
```azurecli
az configure --defaults group=
```
## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Naplók, metrikák és nyomkövetés](spring-cloud-quickstart-logs-metrics-tracing.md)
