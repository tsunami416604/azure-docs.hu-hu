---
title: Rövid útmutató az Azure App Configuration használatának megismeréséhez
description: Rövid útmutató az Azure App Configuration java tavaszi alkalmazásokkal való használatához.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: lcozzens
ms.openlocfilehash: c156d5f1242674adc53a2a813e9b2c140221ecfb
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80245310"
---
# <a name="quickstart-create-a-java-spring-app-with-azure-app-configuration"></a>Rövid útmutató: Java Spring alkalmazás létrehozása az Azure App Konfigurációjával

Ebben a rövid útmutatóban az Azure App Configuration alkalmazást egy Java Spring alkalmazásba építheti be, hogy központosítsa az alkalmazásbeállítások at a kódtól elkülönítve.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- Egy támogatott [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk) a 8-as verzióval.
- [Apache Maven](https://maven.apache.org/download.cgi) 3.0-s vagy újabb verzió.

## <a name="create-an-app-configuration-store"></a>Alkalmazáskonfigurációs tároló létrehozása

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. A következő kulcsérték-párok hozzáadásához válassza a **Configuration Explorer** > + Create**Key-value (Kulcsérték** **létrehozása)** > lehetőséget:

    | Kulcs | Érték |
    |---|---|
    | /application/config.message | helló |

    Egyelőre hagyja üresen a **Címke** és **a Tartalomtípus** mezőt.

7. Kattintson az **Alkalmaz** gombra.

## <a name="create-a-spring-boot-app"></a>Tavaszi rendszerindítási alkalmazás létrehozása

A [tavaszi kezdőbetűvel](https://start.spring.io/) új tavaszi boot projektet hozhat létre.

1. Nyissa meg a következő címet: <https://start.spring.io/>.

1. Adja meg a következő beállításokat:

   - Hozzon létre egy **Maven** projektet **Javával**.
   - Adjon meg egy 2.0-s vagy annál nagyobb **tavaszi rendszerindítási** verziót.
   - Adja meg az alkalmazáshoz tartozó **Group** (Csoport) és **Artifact** (Összetevő) neveket.
   - Adja hozzá a **tavaszi webes** függőséget.

1. Miután megadta az előző beállításokat, válassza **a Projekt létrehozása lehetőséget.** Ha a rendszer kéri, töltse le a projektet a helyi számítógépre.

## <a name="connect-to-an-app-configuration-store"></a>Csatlakozás alkalmazáskonfigurációs tárolóhoz

1. Miután kibontja a fájlokat a helyi rendszeren, az egyszerű Spring Boot alkalmazás készen áll a szerkesztésre. Keresse meg a *pom.xml* fájlt az alkalmazás gyökérkönyvtárában.

1. Nyissa meg a *pom.xml* fájlt egy szövegszerkesztőben, és adja hozzá `<dependencies>`a Spring Cloud Azure Config starter-t a következő listához:

    **Tavaszi felhő 1.1.x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.1.2</version>
    </dependency>
    ```

    **Tavaszi felhő 1.2.x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.2.2</version>
    </dependency>
    ```

1. Hozzon létre egy *MessageProperties.java* nevű új Java-fájlt az alkalmazás csomagkönyvtárában. Adja hozzá a következő sorokat:

    ```java
    package com.example.demo;

    import org.springframework.boot.context.properties.ConfigurationProperties;

    @ConfigurationProperties(prefix = "config")
    public class MessageProperties {
        private String message;

        public String getMessage() {
            return message;
        }

        public void setMessage(String message) {
            this.message = message;
        }
    }
    ```

1. Hozzon létre egy *HelloController.java* nevű új Java-fájlt az alkalmazás csomagkönyvtárában. Adja hozzá a következő sorokat:

    ```java
    package com.example.demo;

    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;

    @RestController
    public class HelloController {
        private final MessageProperties properties;

        public HelloController(MessageProperties properties) {
            this.properties = properties;
        }

        @GetMapping
        public String getMessage() {
            return "Message: " + properties.getMessage();
        }
    }
    ```

1. Nyissa meg a fő alkalmazás `@EnableConfigurationProperties` Java fájlt, és hozzá, hogy ezt a funkciót.

    ```java
    import org.springframework.boot.context.properties.EnableConfigurationProperties;

    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class DemoApplication {
        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
    ```

1. Hozzon létre `bootstrap.properties` egy új fájlt az alkalmazás erőforráskönyvtára alatt, és adja hozzá a következő sorokat a fájlhoz. Cserélje le a mintaértékeket az alkalmazáskonfigurációs tároló megfelelő tulajdonságaira.

    ```CLI
    spring.cloud.azure.appconfiguration.stores[0].name= ${APP_CONFIGURATION_CONNECTION_STRING}
    ```

1. Állítson be egy **APP_CONFIGURATION_CONNECTION_STRING**nevű környezeti változót, és állítsa be az alkalmazáskonfigurációs tároló hozzáférési kulcsára. A parancssorban futtassa a következő parancsot, és indítsa újra a parancssort a módosítás érvénybe léptetésének engedélyezéséhez:

    ```cmd
        setx APP_CONFIGURATION_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    Ha windows PowerShellt használ, futtassa a következő parancsot:

    ```azurepowershell
        $Env:APP_CONFIGURATION_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    MacOS vagy Linux használata esetén futtassa a következő parancsot:

    ```cmd
        export APP_CONFIGURATION_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

## <a name="build-and-run-the-app-locally"></a>Az alkalmazás helyi létrehozása és futtatása

1. Készítsd el a Tavaszi Boot alkalmazást a Mavennel, és futtassa, például:

    ```cmd
    mvn clean package
    mvn spring-boot:run
    ```

2. Az alkalmazás futtatása után a *curl* segítségével tesztelheti az alkalmazást, például:

      ```cmd
      curl -X GET http://localhost:8080/
      ```

    Megjelenik az alkalmazáskonfigurációs tárolóban megadott üzenet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy új App Configuration Store-t, és használta egy Java Spring alkalmazással. További információ: [Spring on Azure](https://docs.microsoft.com/java/azure/spring-framework/). Ha meg szeretné tudni, hogyan engedélyezheti, hogy a Java Spring alkalmazás dinamikusan frissítse a konfigurációs beállításokat, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Dinamikus konfiguráció engedélyezése](./enable-dynamic-configuration-java-spring-app.md)
