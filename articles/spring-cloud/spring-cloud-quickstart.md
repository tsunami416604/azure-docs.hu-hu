---
title: Gyorsútmutató – Az első Azure Spring Cloud-alkalmazás üzembe helyezése
description: Ebben a rövid útmutatóban egy Spring Cloud Hello-alkalmazást helyezünk üzembe az Azure Spring Cloud-on.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 6a7a489367aa7348a3449a72de6889476e5d38db
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2020
ms.locfileid: "89177599"
---
# <a name="quickstart-deploy-your-first-azure-spring-cloud-application"></a>Rövid útmutató: az első Azure Spring Cloud-alkalmazás üzembe helyezése

Ez a rövid útmutató ismerteti, hogyan helyezhet üzembe egy egyszerű Azure Spring Cloud Service-alkalmazást az Azure-ban való futtatáshoz. 

Az oktatóanyagban használt alkalmazás kódja egy egyszerű, rugós Inicializálással készített alkalmazás. A példa elvégzése után az alkalmazás online állapotba kerül, és a Azure Portal használatával felügyelhető.

Ez a rövid útmutató a következőket ismerteti:

> [!div class="checklist"]
> * Alapszintű Spring Cloud-projekt létrehozása
> * Szolgáltatási példány kiépítése
> * Az alkalmazás létrehozása és üzembe helyezése nyilvános végponttal
> * Adatfolyam-naplók valós időben

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* [A JDK 8 telepítése](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Feliratkozás Azure-előfizetésre](https://azure.microsoft.com/free/)
* Választható [Telepítse az Azure CLI 2.0.67 vagy újabb verzióját](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) , és az Azure Spring Cloud bővítményt a paranccsal: `az extension add --name spring-cloud`
* Választható [A Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) és a [Bejelentkezés](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in) telepítése

## <a name="generate-a-spring-cloud-hello-project"></a>Spring Cloud Hello-projekt létrehozása

Kezdje a [Spring inicializáló](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.3.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin) használatával egy olyan minta projekt létrehozásához, amely az Azure Spring Cloud ajánlott függőségeivel rendelkezik. Az alábbi képen a projekt inicializálási beállítása látható.
```url
https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.3.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin
```

  ![Inicializálás lap](media/spring-cloud-quickstart-java/initializr-page.png)

1. Kattintson a **generált** elemre, ha az összes függőség be van állítva. Töltse le és csomagolja ki a csomagot, majd hozzon létre egy webes vezérlőt egy egyszerű webalkalmazáshoz a `src/main/java/com/example/hellospring/HelloController.java` következőképpen:

    ```java
    package com.example.hellospring;
    
    import org.springframework.web.bind.annotation.RestController;
    import org.springframework.web.bind.annotation.RequestMapping;
    
    @RestController
    public class HelloController {
    
        @RequestMapping("/")
        public String index() {
            return "Greetings from Azure Spring Cloud!";
        }
    
    }
    ```
## <a name="provision-an-instance-of-azure-spring-cloud"></a>Azure Spring Cloud-példány kiépítése

Az alábbi eljárás az Azure Spring Cloud egy példányát hozza létre a Azure Portal használatával.

1. Az új lapon nyissa meg a [Azure Portal](https://ms.portal.azure.com/). 

2. A felső keresőmezőbe keressen az *Azure Spring Cloud*kifejezésre.

3. Válassza az *Azure Spring Cloud* lehetőséget az eredmények közül.

    ![ASC ikon kezdete](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Az Azure Spring Cloud oldalon kattintson a **+ Hozzáadás**gombra.

    ![ASC ikon hozzáadása](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Töltse ki az űrlapot az Azure Spring Cloud **create** oldalon.  Vegye figyelembe a következő irányelveket:
    - **Előfizetés**: válassza ki azt az előfizetést, amelyet számlázni szeretne az erőforráshoz.
    - **Erőforráscsoport**: új erőforráscsoportok létrehozása új erőforrásokhoz az ajánlott eljárás. Ezt a későbbi lépések során fogjuk használni **\<resource group name\>** .
    - **Szolgáltatás adatai/neve**: adja meg a **\<service instance name\>** .  A névnek 4 – 32 karakter hosszúnak kell lennie, és csak kisbetűket, számokat és kötőjeleket tartalmazhat.  A szolgáltatás nevének első karakterének betűnek kell lennie, és az utolsó karakternek betűnek vagy számnak kell lennie.
    - **Hely**: válassza ki a szolgáltatási példány régióját.

    ![ASC-portál indítása](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Kattintson az **Áttekintés és létrehozás** elemre.

## <a name="build-and-deploy-the-app"></a>Az alkalmazás létrehozása és üzembe helyezése
    
#### <a name="cli"></a>[Parancssori felület](#tab/Azure-CLI)
Az alábbi eljárás az Azure CLI használatával hozza létre és telepíti az alkalmazást. Hajtsa végre a következő parancsot a projekt gyökerében.

1. A projekt összeállítása a Maven használatával:

    ```console
    mvn clean package -DskipTests
    ```

1. (Ha még nem telepítette) Telepítse az Azure Spring Cloud-bővítményt az Azure CLI-hez:

    ```azurecli
    az extension add --name spring-cloud
    ```
    
1. Hozza létre az alkalmazást a hozzárendelt nyilvános végponttal:

    ```azurecli
    az spring-cloud app create -n hellospring -s <service instance name> -g <resource group name> --is-public
    ```

1. Telepítse az alkalmazás jar-fájlját:

    ```azurecli
    az spring-cloud app deploy -n hellospring -s <service instance name> -g <resource group name> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar
    ```
    
1. Az alkalmazás üzembe helyezésének befejezése néhány percet vesz igénybe. Annak ellenőrzéséhez, hogy telepítve van-e, nyissa meg az **alkalmazások** panelt a Azure Portal. Ekkor meg kell jelennie az alkalmazás állapotának.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

Az alábbi eljárás az Azure Spring Cloud IntelliJ beépülő moduljának használatával helyezi üzembe a minta alkalmazást a IntelliJ-ÖTLETben.  

### <a name="import-hello-project"></a>Hello-projekt importálása

1. Nyissa meg az IntelliJ **üdvözlő** párbeszédpanelt, és válassza a **Projekt importálása** elemet az Importálás varázsló megnyitásához.
1. Válassza a `hellospring` mappa lehetőséget.

    ![Projekt importálása](media/spring-cloud-quickstart-java/intellij-new-project.png)

### <a name="deploy-the-app"></a>Az alkalmazás üzembe helyezése
Az Azure-ba való üzembe helyezéshez be kell jelentkeznie az Azure-fiókjával, és ki kell választania az előfizetését.  A bejelentkezés részleteiért lásd: [telepítés és bejelentkezés](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

1. Kattintson a jobb gombbal a projektre a IntelliJ Project Explorerben, és válassza az **Azure**-  ->  **üzembe helyezés az Azure Spring Cloud**-ban lehetőséget.

    [![Üzembe helyezés az Azure ](media/spring-cloud-quickstart-java/intellij-deploy-azure-1.png) 1-ben](media/spring-cloud-quickstart-java/intellij-deploy-azure-1.png#lightbox)

1. Fogadja el az alkalmazás nevét a **név** mezőben. A **név** a konfigurációra hivatkozik, nem az alkalmazás nevére. A felhasználóknak általában nem kell módosítaniuk.
1. Az összetevő **szövegmezőben** válassza a *hellospring-0.0.1-Snapshot. jar*elemet.
1. Az **előfizetés** szövegmezőben ellenőrizze az előfizetését.
1. A **Spring Cloud** szövegmezőben válassza ki azt az Azure Spring Cloud-példányt, amelyet az [Azure Spring Cloud-példány kiépítése](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-provision-service-instance)című részben hozott létre.
1. Az *engedélyezéshez*állítsa be a **nyilvános végpontot** .
1. Az **alkalmazás:** szövegmezőben válassza az **alkalmazás létrehozása...** lehetőséget.
1. Adja meg a *hellospring*, majd kattintson **az OK**gombra.

    [![Üzembe helyezés az Azure-ban ok ](media/spring-cloud-quickstart-java/intellij-deploy-to-azure.png)](media/spring-cloud-quickstart-java/intellij-deploy-to-azure.png#lightbox)

1. A telepítés elindításához kattintson a **Futtatás** gombra az **Azure Spring Cloud app üzembe helyezése** párbeszédpanel alján. A beépülő modul futtatja a parancsot `mvn package` az `hellospring` alkalmazáson, és telepíti a parancs által generált jar-t `package` .
---

Az üzembe helyezés befejezése után az alkalmazás a következő címen érhető el: `https://<service instance name>-hellospring.azuremicroservices.io/` .

  [![Alkalmazás elérése böngészőből ](media/spring-cloud-quickstart-java/access-app-browser.png)](media/spring-cloud-quickstart-java/access-app-browser.png#lightbox)

## <a name="streaming-logs-in-real-time"></a>Valós idejű folyamatos átviteli naplók

#### <a name="cli"></a>[Parancssori felület](#tab/Azure-CLI)

A következő parancs használatával valós idejű naplókat kaphat az alkalmazásból.

```azurecli
az spring-cloud app logs -n hellospring -s <service instance name> -g <resource group name> --lines 100 -f

```
A naplók az eredmények között jelennek meg:

[![Folyamatos átviteli naplók ](media/spring-cloud-quickstart-java/streaming-logs.png)](media/spring-cloud-quickstart-java/streaming-logs.png#lightbox)

>[!TIP]
> `az spring-cloud app logs -h`A használatával több paramétert és a log stream funkcióit is megismerheti.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

1. Válassza az **Azure Explorer**, majd a **Spring Cloud**elemet.
1. Kattintson a jobb gombbal a futó alkalmazásra.
1. Válassza a **folyamatos átviteli naplók** lehetőséget a legördülő listából.
1. Válassza a példány lehetőséget.

    [![Adatfolyam-naplók ](media/spring-cloud-quickstart-java/intellij-get-streaming-logs.png) kiválasztása](media/spring-cloud-quickstart-java/intellij-get-streaming-logs.png)

1. A folyamatos átviteli napló a kimeneti ablakban jelenik meg.

    [![Adatfolyam-napló kimenete ](media/spring-cloud-quickstart-java/intellij-streaming-logs-output.png)](media/spring-cloud-quickstart-java/intellij-streaming-logs-output.png)
---

A speciális naplók elemzési szolgáltatásaihoz látogasson el a **naplók** lapra a [Azure Portal](https://portal.azure.com/)menüjében. A naplók itt néhány perc késéssel rendelkeznek.

[![Naplók Analytics ](media/spring-cloud-quickstart-java/logs-analytics.png)](media/spring-cloud-quickstart-java/logs-analytics.png#lightbox)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Az előző lépésekben Azure-erőforrásokat hozott létre egy erőforráscsoportban. Ha nem várható, hogy a jövőben szüksége lenne ezekre az erőforrásokra, törölje az erőforráscsoportot a portálról, vagy futtassa a következő parancsot a Cloud Shellban:
```azurecli
az group delete --name <your resource group name; for example: hellospring-1558400876966-rg> --yes
```

Ennek a rövid útmutatónak a segítségével megtanulta a következőket:

> [!div class="checklist"]
> * Alapszintű Azure Spring Cloud-projekt létrehozása
> * Szolgáltatási példány kiépítése
> * Az alkalmazás létrehozása és üzembe helyezése nyilvános végponttal
> * Valós idejű folyamatos átviteli naplók
## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [A szolgáltatás létrehozása és futtatása](spring-cloud-quickstart-sample-app-introduction.md)

További minták érhetők el a GitHubon: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
