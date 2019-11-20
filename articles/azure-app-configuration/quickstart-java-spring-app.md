---
title: Útmutató az Azure-alkalmazások konfigurációjának használatáról | Microsoft Docs
description: Útmutató az Azure-alkalmazások konfigurálásához a Java Spring Apps használatával.
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
ms.openlocfilehash: e8f6f9ca610c515deca6ed1bdbee54f40cacf427
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184939"
---
# <a name="quickstart-create-a-java-spring-app-with-azure-app-configuration"></a>Rövid útmutató: Java Spring-alkalmazás létrehozása az Azure app Configuration szolgáltatással

Ebben a rövid útmutatóban beépíti az Azure-alkalmazások konfigurációját egy Java Spring-alkalmazásba, hogy központilag központosítsa az alkalmazás-beállítások tárolási és kezelési beállításait a kódból.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- A 8-as verzióval támogatott [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk) .
- Az [Apache Maven](https://maven.apache.org/download.cgi) 3,0-es vagy újabb verziója.

## <a name="create-an-app-configuration-store"></a>Alkalmazás-konfigurációs tároló létrehozása

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Válassza a **Configuration Explorer** >  **+ Létrehozás** lehetőséget a következő kulcs-érték párok hozzáadásához:

    | Paraméter | Érték |
    |---|---|
    | /application/config.message | Üdvözöljük! |

    Most hagyja üresen a **címke** és a **tartalom típusát** .

## <a name="create-a-spring-boot-app"></a>Spring boot-alkalmazás létrehozása

A [Spring inicializáló](https://start.spring.io/) használatával új Spring boot-projektet hozhat létre.

1. Nyissa meg a következő címet: <https://start.spring.io/>.

2. A következő beállításokat kell megadnia:

   * **Maven** -projekt létrehozása **Javával**.
   * Olyan **Spring boot** -verziót válasszon, amely egyenlő vagy nagyobb, mint 2,0.
   * Adja meg az alkalmazás **csoport** -és **lelet** -nevét.
   * Adja hozzá a **webes** függőséget.

3. Az előző beállítások megadása után válassza a **projekt létrehozása**lehetőséget. Ha a rendszer kéri, töltse le a projektet egy elérési útra a helyi számítógépen.

## <a name="connect-to-an-app-configuration-store"></a>Kapcsolódás alkalmazás-konfigurációs tárolóhoz

1. Miután kicsomagolta a fájlokat a helyi rendszeren, az egyszerű Spring boot-alkalmazás készen áll a szerkesztésre. Keresse meg a *Pom. XML* fájlt az alkalmazás gyökérkönyvtárában.

2. Nyissa meg a *Pom. XML* fájlt egy szövegszerkesztőben, és adja hozzá a Spring Cloud Azure config Starter-t a `<dependencies>`listájához:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.1.0.M5</version>
    </dependency>
    ```

3. Hozzon létre egy új, *MessageProperties. Java* nevű Java-fájlt az alkalmazás csomag könyvtárába. Adja hozzá a következő sorokat:

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

4. Hozzon létre egy új, *HelloController. Java* nevű Java-fájlt az alkalmazás csomag könyvtárába. Adja hozzá a következő sorokat:

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

5. Nyissa meg az alkalmazás fő Java-fájlját, és adja hozzá a `@EnableConfigurationProperties` a funkció engedélyezéséhez.

    ```java
    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class AzureConfigApplication {
        public static void main(String[] args) {
            SpringApplication.run(AzureConfigApplication.class, args);
        }
    }
    ```

6. Hozzon létre egy `bootstrap.properties` nevű új fájlt az alkalmazás erőforrások könyvtára alatt, és adja hozzá a következő sorokat a fájlhoz. Cserélje le a mintavételi értékeket az alkalmazás konfigurációs tárolójának megfelelő tulajdonságaira.

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].connection-string=[your-connection-string]
    ```

## <a name="build-and-run-the-app-locally"></a>Az alkalmazás helyi létrehozása és futtatása

1. Készítse elő a Spring boot-alkalmazást a Maven használatával, és futtassa, például:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```
2. Az alkalmazás futása után a *curl* használatával tesztelheti az alkalmazást, például:

      ```shell
      curl -X GET http://localhost:8080/
      ```
    Megjelenik az alkalmazás konfigurációs tárolójában megadott üzenet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy új alkalmazás-konfigurációs tárolót, amelyet egy Java Spring-alkalmazással használt. További információ: [Spring on Azure](https://docs.microsoft.com/java/azure/spring-framework/). Ha szeretné megtudni, hogyan használható az Azure felügyelt identitása az alkalmazás-konfigurációhoz való hozzáférés egyszerűsítéséhez, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Felügyelt identitások integrációja](./howto-integrate-azure-managed-service-identity.md)
