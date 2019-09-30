---
title: Gyors üzembe helyezési útmutató a Spring boot – Azure app Configuration szolgáltatáshoz | Microsoft Docs
description: Gyors üzembe helyezési útmutató a Spring boot-alkalmazásokhoz és az Azure-alkalmazások konfigurációjában való kezeléséhez
services: azure-app-configuration
documentationcenter: ''
author: mrm9084
manager: zhenlwa
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: Spring Boot
ms.workload: tbd
ms.date: 09/26/2019
ms.author: mametcal
ms.openlocfilehash: fe943913f9d33923438efb91630c5a85c4d1b748
ms.sourcegitcommit: 6013bacd83a4ac8a464de34ab3d1c976077425c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71687184"
---
# <a name="quickstart-add-feature-flags-to-a-spring-boot-app"></a>Gyors útmutató: Szolgáltatás-jelzők hozzáadása Spring boot-alkalmazáshoz

Ebben a rövid útmutatóban beépítheti az Azure-alkalmazások konfigurációját egy Spring boot-webalkalmazásba, amely a szolgáltatások felügyeletének teljes körű megvalósítását hozza létre. Az alkalmazás konfigurációs szolgáltatásával központilag tárolhatja az összes funkció jelzőjét, és szabályozhatja az állapotukat.

A tavaszi rendszerindítási szolgáltatás felügyeleti kódtárai kiterjesztik a keretrendszert a szolgáltatás teljes körű támogatásával. Ezek a kódtárak **nem** függenek az Azure-libries. Zökkenőmentesen integrálva vannak az alkalmazások konfigurációjával a Spring boot-konfiguráció szolgáltatóján keresztül.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- Támogatott [Java Development Kit SDK](https://docs.microsoft.com/java/azure/jdk) a 8-as verzióval.
- Az [Apache Maven](https://maven.apache.org/download.cgi) 3,0-es vagy újabb verziója.

## <a name="create-an-app-configuration-store"></a>Alkalmazás-konfigurációs tároló létrehozása

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Válassza a **szolgáltatás-kezelő** >  **+ Létrehozás** lehetőséget a következő funkció-jelzők hozzáadásához:

    | Kulcs | State |
    |---|---|
    | Bétaverzió | Ki |

## <a name="create-a-spring-boot-app"></a>Spring boot-alkalmazás létrehozása

A [Spring inicializáló](https://start.spring.io/) használatával új Spring boot-projektet hozhat létre.

1. Nyissa meg a következő címet: <https://start.spring.io/>.

2. A következő beállításokat kell megadnia:

   - **Maven** -projekt létrehozása **Javával**.
   - Olyan **Spring boot** -verziót válasszon, amely egyenlő vagy nagyobb, mint 2,0.
   - Adja meg az alkalmazás **csoport** -és **lelet** -nevét.
   - Adja hozzá a **webes** függőséget.

3. Az előző beállítások megadása után válassza a **projekt létrehozása**lehetőséget. Ha a rendszer kéri, töltse le a projektet egy elérési útra a helyi számítógépen.

## <a name="add-feature-management"></a>Szolgáltatások felügyeletének hozzáadása

1. Miután kicsomagolta a fájlokat a helyi rendszeren, az egyszerű Spring boot-alkalmazás készen áll a szerkesztésre. Keresse meg a *Pom. XML* fájlt az alkalmazás gyökérkönyvtárában.

2. Nyissa meg a *Pom. XML* fájlt egy szövegszerkesztőben, és adja hozzá a Spring Cloud Azure config Starter és a features Management szolgáltatást a `<dependencies>` listájához:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.1.0.M4</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-feature-management-web</artifactId>
        <version>1.1.0.M4</version>
    </dependency>
    <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    ```

> [!Note]
> A nem webes szolgáltatások felügyeleti könyvtára nem függ a Spring-web-től. Tekintse meg a további [dokumentumokat](https://github.com/microsoft/spring-cloud-azure/tree/master/spring-cloud-azure-feature-management) a különbségekért. Továbbá, ha nem használja az alkalmazás konfigurációját, tekintse meg a [Feature Flag deklarációját](https://github.com/microsoft/spring-cloud-azure/tree/master/spring-cloud-azure-feature-management#feature-flag-declaration).

## <a name="connect-to-an-app-configuration-store"></a>Kapcsolódás alkalmazás-konfigurációs tárolóhoz

1. Nyissa meg `bootstrap.properties`, amely az alkalmazás erőforrások könyvtára alatt található, és adja hozzá a következő sorokat a fájlhoz. Adja hozzá az alkalmazás konfigurációs adatait.

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].name= ${APP_CONFIGURATION_CONNECTION_STRING}
    ```

2. A konfigurációs tárolóhoz tartozó alkalmazás-konfigurációs portálon nyissa meg a hozzáférési kulcsokat. Válassza a csak olvasható kulcsok lapot. Ezen a lapon másolja át a kapcsolatok egyik karakterláncának értékét, és vegye fel új környezeti változóként `APP_CONFIGURATION_CONNECTION_STRING` nevű változóval.

3. Nyissa meg az alkalmazás fő Java-fájlját, és adja hozzá `@EnableConfigurationProperties` a szolgáltatást a funkció engedélyezéséhez.

    ```java
    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class AzureConfigApplication {
        public static void main(String[] args) {
            SpringApplication.run(AzureConfigApplication.class, args);
        }
    }
    ```

4. Hozzon létre egy új, *HelloController. Java* nevű Java-fájlt az alkalmazás csomag könyvtárába. Adja hozzá a következő sorokat:

    ```java
    @Controller
    @ConfigurationProperties("controller")
    public class HelloController {

        private FeatureManager featureManager;

        public HelloController(FeatureManager featureManager) {
            this.featureManager = featureManager;
        }

        @GetMapping("/welcome")
        public String mainWithParam(Model model) {
            model.addAttribute("Beta", featureManager.isEnabled("Beta"));
            return "welcome";
        }
    }
    ```

5. Hozzon létre egy *üdvözlő. html* nevű új HTML-fájlt az alkalmazás sablonok könyvtára. Adja hozzá a következő sorokat:

    ```html
    <!DOCTYPE html>
    <html lang="en" xmlns:th="http://www.thymeleaf.org">
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
        <title>Feature Management with Spring Cloud Azure</title>

        <link rel="stylesheet" href="/css/main.css">
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">

        <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js" integrity="sha384-UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1" crossorigin="anonymous"></script>
        <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js" integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM" crossorigin="anonymous"></script>

    </head>
    <body>
        <header>
        <!-- Fixed navbar -->
        <nav class="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
            <a class="navbar-brand" href="#">TestFeatureFlags</a>
            <button class="navbar-toggler" aria-expanded="false" aria-controls="navbarCollapse" aria-label="Toggle navigation" type="button" data-target="#navbarCollapse" data-toggle="collapse">
            <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarCollapse">
            <ul class="navbar-nav mr-auto">
                <li class="nav-item active">
                <a class="nav-link" href="#">Home <span class="sr-only">(current)</span></a>
                </li>
                <li class="nav-item" th:if="${Beta}">
                <a class="nav-link" href="#">Beta</a>
                </li>
                <li class="nav-item">
                <a class="nav-link" href="#">Privacy</a>
                </li>
            </ul>
            </div>
        </nav>
        </header>
        <div class="container body-content">
            <h1 class="mt-5">Welcome</h1>
            <p>Learn more about <a href="https://github.com/microsoft/spring-cloud-azure/blob/master/spring-cloud-azure-feature-management/README.md">Feature Management with Spring Cloud Azure</a></p>

        </div>
        <footer class="footer">
            <div class="container">
            <span class="text-muted">&copy; 2019 - Projects</span>
        </div>

        </footer>
    </body>
    </html>

    ```

6. Hozzon létre egy új, CSS nevű mappát a statikus és a benne található új CSS-fájl, a *Main. css*néven. Adja hozzá a következő sorokat:

    ```css
    html {
    position: relative;
    min-height: 100%;
    }
    body {
    margin-bottom: 60px;
    }
    .footer {
    position: absolute;
    bottom: 0;
    width: 100%;
    height: 60px;
    line-height: 60px;
    background-color: #f5f5f5;
    }

    body > .container {
    padding: 60px 15px 0;
    }

    .footer > .container {
    padding-right: 15px;
    padding-left: 15px;
    }

    code {
    font-size: 80%;
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Az alkalmazás helyi létrehozása és futtatása

1. Készítse elő a Spring boot-alkalmazást a Maven használatával, és futtassa, például:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

2. Nyisson meg egy böngészőablakot, és `https://localhost:8080`nyissa meg a következőt:, amely a helyileg üzemeltetett webalkalmazás alapértelmezett URL-címe.

    ![Gyorsindítás alkalmazás elindítása helyi](./media/quickstarts/spring-boot-feature-flag-local-before.png)

3. Az alkalmazás konfigurációs portálján válassza a **Feature Manager**elemet, és módosítsa a **bétaverzió** **állapotát a következőre:**

    | Kulcs | State |
    |---|---|
    | Bétaverzió | Bekapcsolva |

4. A böngésző oldalának frissítésével tekintheti meg az új konfigurációs beállításokat.

    ![Gyorsindítás alkalmazás elindítása helyi](./media/quickstarts/spring-boot-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy új alkalmazás-konfigurációs tárolót, és felhasználta egy Spring boot-webalkalmazás funkcióinak kezeléséhez a [Feature Management librarys](https://go.microsoft.com/fwlink/?linkid=2074664)használatával.

- További információ a [szolgáltatások kezeléséről](./concept-feature-management.md).
- [Szolgáltatás-jelzők kezelése](./manage-feature-flags.md).
- [A Feature Flags szolgáltatást egy Spring boot Core-alkalmazásban használhatja](./use-feature-flags-spring-boot.md).
