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
ms.openlocfilehash: 83dc6436c69038a43f32588d485790a701c2e18d
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57549255"
---
# <a name="quickstart-create-a-java-spring-app-with-app-configuration"></a>Gyors útmutató: Egy Java Spring-alkalmazás létrehozása az alkalmazás konfigurációja

Az Azure App konfigurálása felügyelt konfigurációs szolgáltatása az Azure-ban. Ez lehetővé teszi, hogy könnyedén tárolhatja és kezelheti a kódot egy helyet, amely választja el az alkalmazás beállításait. Ez a rövid útmutató bemutatja, hogyan belefoglalhatja a szolgáltatást egy Java Spring alkalmazásba.

A rövid útmutató lépései bármilyen szövegszerkesztővel elvégezhetők. A [Visual Studio Code](https://code.visualstudio.com/) például jó választás lehet, és Windows, macOS és Linux platformokon is használható.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez telepítse egy támogatott [Java fejlesztői készlet (JDK)](https://aka.ms/azure-jdks) 8-verzióval és [Apache Maven](https://maven.apache.org/) a 3.0-s verzió vagy újabb.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Hozzon létre egy alkalmazást a konfigurációs adattároló

1. Hozzon létre egy új alkalmazás-konfigurációs adattároló, először jelentkezzen be a [az Azure portal](https://aka.ms/azconfig/portal). Kattintson az **+ Erőforrás létrehozása** elemre az oldal bal felső oldalán. Az a **keresés a piactéren** szövegmezőbe írja be **Alkalmazáskonfiguráció** nyomja le az ENTER **Enter**.

    ![Keresse meg az alkalmazás konfigurációja](./media/quickstarts/azure-app-configuration-new.png)

2. Kattintson a **Alkalmazáskonfiguráció** a keresési eredmények között, majd **létrehozás**.

3. Az a **Alkalmazáskonfiguráció** > **létrehozás** lap, adja meg a következő beállításokat:

    | Beállítás | Ajánlott érték | Leírás |
    |---|---|---|
    | **Erőforrás neve** | Globálisan egyedi név | Adjon meg egy egyedi erőforráscsoport nevét az alkalmazás-konfigurációs tár erőforrást. A névnek 1 és 63 karakter közötti sztringnek kell lennie, és kizárólag számokat, betűket és a `-` karaktert tartalmazhatja. A név nem kezdődhet a `-` karakterrel, és az egymást követő `-` karakterek nem érvényesek.  |
    | **Előfizetés** | Az Ön előfizetése | Válassza ki az alkalmazás konfigurációjának tesztelése használni kívánt Azure-előfizetést. Ha a fiókja csak egyetlen előfizetéssel rendelkezik, automatikusan ez lesz kiválasztva, és az **Előfizetés** legördülő lista nem jelenik meg. |
    | **Erőforráscsoport** | *AppConfigTestResources* | Válassza ki vagy hozzon létre egy erőforráscsoportot az alkalmazás konfigurációs tároló-erőforrás. Ez a csoport több erőforrás rendszerezéséhez hasznos, amelyeket az erőforráscsoport törlésekor egyszerre törölhet. További információk: [Using Resource groups to manage your Azure resources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez). |
    | **Hely** | *USA középső RÉGIÓJA* | A **Hely** beállítással megadhatja a földrajzi helyet, ahol a SignalR-erőforrást üzemeltetni kívánja. A legjobb teljesítmény érdekében azt javasoljuk, hogy az erőforrást ugyanabban a régióban hozza létre, mint az alkalmazás többi összetevőjét. |

    ![Hozzon létre egy alkalmazást a konfigurációs adattároló](./media/quickstarts/azure-app-configuration-create.png)

4. Kattintson a **Create** (Létrehozás) gombra. Az üzembe helyezés befejezése eltarthat néhány percig.

5. Az üzembe helyezés befejezése után kattintson a **beállítások** > **Tárelérési kulcsok**. Jegyezze fel a bármelyik elsődleges csak olvasható vagy elsődleges írási-olvasási kulcs kapcsolati karakterláncára. Használhatjuk a ez később az imént létrehozott alkalmazás a konfigurációs adattároló folytatott kommunikációhoz az alkalmazás konfigurálása. A kapcsolati karakterlánc formátuma a következő:

        Endpoint=<your_endpoint>;Id=<your_id>;Secret=<your_secret>

    Szüksége lesz a teljes sztring használhatja az alkalmazásban.

6. Kattintson a **kulcs/érték Explorer** és **+ létrehozás** a következő kulcs-érték párok hozzáadásához:

    | Kulcs | Érték |
    |---|---|
    | /application/config.message | Üdvözöljük! |

    Hagyja meg **címke** és **tartalomtípus** most üres.

## <a name="create-a-spring-boot-app"></a>Hozzon létre egy Spring Boot alkalmazás

Használhatja a [Spring Initializr](https://start.spring.io/) egy új Spring Boot-projekt létrehozása.

1. Nyissa meg a következő címet: <https://start.spring.io/>.

2. Adja meg a következő beállításokat:

   * Hozzon létre egy **Maven** a projekt **Java**.
   * Adjon meg egy **Spring Boot** egyenlő vagy nagyobb, mint a 2.0-s verzióját.
   * Adja meg a **csoport** és **összetevő** az alkalmazás nevét.
   * Adja hozzá a **webes** függőség.

3. Kattintson a fenti beállítások megadását, **készítése a projekt**. Amikor a rendszer kéri, töltse le a projekt egy elérési utat a helyi számítógépen.

## <a name="connect-to-app-configuration-store"></a>Csatlakozhat az alkalmazás a konfigurációs adattároló

1. Miután kicsomagolta a fájlok a helyi számítógépen, az egyszerű Spring Boot-alkalmazás szerkesztésre készen áll. Keresse meg a *pom.xml* fájlt az alkalmazás gyökérkönyvtárában.

2. Nyissa meg a *pom.xml* fájlt egy szövegszerkesztőben, és adja hozzá a Spring Cloud Azure Config alapszintű listájának `<dependencies>`:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.1.0.M1</version>
    </dependency>
    ```

3. Hozzon létre egy új Java-fájlt *MessageProperties.java* az alkalmazás a csomag könyvtárában. Adja hozzá az alábbi sorokat.

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

4. Hozzon létre egy új Java-fájlt *HelloController.java* az alkalmazás a csomag könyvtárában. Adja hozzá az alábbi sorokat.

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

6. Hozzon létre egy új fájlt `bootstrap.properties` az erőforrásokat a könyvtárban az alkalmazás, és adja hozzá a következő sorokat a fájl, és cserélje le a megfelelő tulajdonságokat az alkalmazás konfigurációs tárához tartozó mintaértékeket.

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].connection-string=[your-connection-string]
    ```

## <a name="build-and-run-the-app-locally"></a>Hozhat létre, és az alkalmazás helyileg történő futtatása

1. A Mavennel a Spring Boot-alkalmazás létrehozásához és futtatásához. Példa:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```
2. Miután az alkalmazás fut, *curl* segítségével az alkalmazást; például:

      ```shell
      curl -X GET http://localhost:8080/
      ```
    Megtekintheti az üzenetet, amely az alkalmazás konfigurációs tárolóba megadott.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ez a rövid útmutatóban egy új alkalmazás-konfigurációs adattároló létrehozta és egy Java Spring alkalmazással használta azt. Látogasson el [Spring az Azure kezdőlapjának](https://docs.microsoft.com/java/azure/spring-framework/) , további információt.

Alkalmazáskonfiguráció használatával kapcsolatos további információkért folytassa a következő oktatóanyag azt mutatja be a hitelesítést.

> [!div class="nextstepaction"]
> [Az Azure-erőforrások integráció felügyelt identitásokból](./integrate-azure-managed-service-identity.md)