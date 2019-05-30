---
title: 'Gyors útmutató: ismerje meg, hogyan használhatja az Alkalmazáskonfigurációt Azure |} A Microsoft Docs'
description: Gyors üzembe helyezés az Azure-alkalmazások konfigurálása használata Java Spring-alkalmazásokkal.
services: azure-app-configuration
documentationcenter: ''
author: yidon
manager: jeffya
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: Spring
ms.workload: tbd
ms.date: 01/08/2019
ms.author: yidon
ms.openlocfilehash: a91c61edd773b5742b092f5d72a5a22f1d90e63b
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393546"
---
# <a name="quickstart-create-a-java-spring-app-with-app-configuration"></a>Gyors útmutató: Egy Java Spring-alkalmazás létrehozása az alkalmazás konfigurációja

Az Azure App konfigurálása felügyelt konfigurációs szolgáltatása az Azure-ban. Könnyedén tárolhatja, és a egy helyet, amely választja el az alkalmazás beállításait kezelheti a kódból használhatja azt. Ez a rövid útmutató bemutatja, hogyan belefoglalhatja a szolgáltatást egy Java Spring alkalmazásba.

Bármely Kódszerkesztő segítségével ebben a rövid útmutatóban található lépések elvégzése. [A Visual Studio Code](https://code.visualstudio.com/) kiváló lehetőség a Windows, macOS és Linux platformokon az érhető el.

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató elvégzéséhez telepítse egy támogatott [Java fejlesztői készlet (JDK)](https://docs.microsoft.com/java/azure/jdk) 8-verzióval és [Apache Maven](https://maven.apache.org/) a 3.0-s verzió vagy újabb.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Hozzon létre egy alkalmazást a konfigurációs adattároló

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Válassza ki **konfigurációs Explorer** >  **+ létrehozás** a következő kulcs-érték párok hozzáadásához:

    | Kulcs | Érték |
    |---|---|
    | /application/config.message | Üdvözöljük! |

    Hagyja **címke** és **tartalomtípus** most üres.

## <a name="create-a-spring-boot-app"></a>Hozzon létre egy Spring Boot alkalmazás

Használja a [Spring Initializr](https://start.spring.io/) egy új Spring Boot-projekt létrehozása.

1. Nyissa meg a következő címet: <https://start.spring.io/>.

2. Adja meg a következő beállításokat:

   * Hozzon létre egy **Maven** a projekt **Java**.
   * Adjon meg egy **Spring Boot** egyenlő vagy nagyobb, mint a 2.0-s verzióját.
   * Adja meg a **csoport** és **összetevő** az alkalmazás nevét.
   * Adja hozzá a **webes** függőség.

3. Után az előző beállítások megadásához válassza **készítése a projekt**. Amikor a rendszer kéri, töltse le a projekt egy elérési utat a helyi számítógépen.

## <a name="connect-to-an-app-configuration-store"></a>Egy alkalmazás a konfigurációs adattároló csatlakozni

1. Miután, bontsa ki a helyi számítógépen, az egyszerű Spring Boot-alkalmazás készen áll a szerkesztése. Keresse meg a *pom.xml* fájlt az alkalmazás gyökérkönyvtárában.

2. Nyissa meg a *pom.xml* fájlt egy szövegszerkesztőben, és adja hozzá a Spring Cloud Azure Config alapszintű listájának `<dependencies>`:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.1.0.M3</version>
    </dependency>
    ```

3. Hozzon létre egy új Java-fájlt *MessageProperties.java* az alkalmazás a csomag könyvtárában. Adja hozzá a következő sorokat:

    ```java
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

4. Hozzon létre egy új Java-fájlt *HelloController.java* az alkalmazás a csomag könyvtárában. Adja hozzá a következő sorokat:

    ```java
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

5. Nyissa meg az alkalmazás fő Java fájlt, és adja hozzá `@EnableConfigurationProperties` a funkció engedélyezéséhez.

    ```java
    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class AzureConfigApplication {
        public static void main(String[] args) {
            SpringApplication.run(AzureConfigApplication.class, args);
        }
    }
    ```

6. Hozzon létre egy új fájlt `bootstrap.properties` az alkalmazás az erőforrásokat a könyvtárban, és adja hozzá a következő sorokat a fájl. Cserélje le a megfelelő tulajdonságokat az alkalmazás konfigurációs tárához tartozó mintaértékeket.

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].connection-string=[your-connection-string]
    ```

## <a name="build-and-run-the-app-locally"></a>Hozhat létre, és az alkalmazás helyileg történő futtatása

1. A Mavennel a Spring Boot-alkalmazás létrehozása és futtatása, pl.:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```
2. Miután az alkalmazás fut, a *curl* segítségével az alkalmazást, például:

      ```shell
      curl -X GET http://localhost:8080/
      ```
    Az üzenetet kap, az alkalmazás a konfigurációs adattároló megadott.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy új alkalmazás-konfigurációs adattároló, és egy Java Spring alkalmazással használta azt. További információkért lásd: [Spring az Azure-ban](https://docs.microsoft.com/java/azure/spring-framework/).

Ismerje meg, hogyan használhatja az Alkalmazáskonfigurációt, folytassa a következő oktatóanyag azt mutatja be a hitelesítést.

> [!div class="nextstepaction"]
> [Felügyelt identitás-integráció](./howto-integrate-azure-managed-service-identity.md)
