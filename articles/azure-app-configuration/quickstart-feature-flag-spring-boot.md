---
title: Rövid útmutató a szolgáltatásjelzők tavaszi rendszerindításhoz való hozzáadásához az Azure App Konfigurációjával
description: Funkciójelzők hozzáadása a tavaszi rendszerindítási alkalmazásokhoz és kezelésük az Azure App Konfigurációja szolgáltatással
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 04/13/2020
ms.author: lcozzens
ms.openlocfilehash: 398005b37660cfa68936a0c8b3a3d90da8160e49
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309073"
---
# <a name="quickstart-add-feature-flags-to-a-spring-boot-app"></a>Rövid útmutató: Szolgáltatásjelzők hozzáadása tavaszi rendszerindítási alkalmazáshoz

Ebben a rövid útmutatóban az Azure App Configuration alkalmazást egy tavaszi rendszerindítási webalkalmazásba építheti be a szolgáltatáskezelés teljes körű megvalósításának létrehozásához. Az Alkalmazáskonfigurációs szolgáltatás segítségével központilag tárolhatja az összes funkciójelzőt, és szabályozhatja azok állapotát.

A tavaszi rendszerindítási szolgáltatáskezelő kódtárak átfogó funkciójelző-támogatással bővítik a keretrendszert. Ezek a könyvtárak **nem** függ az Azure-kódtárak. Zökkenőmentesen integrálhatók az alkalmazáskonfigurációval a Spring Boot konfigurációs szolgáltatón keresztül.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* Egy támogatott [Java Development Kit SDK](https://docs.microsoft.com/java/azure/jdk) 8-as verzióval.
* [Apache Maven](https://maven.apache.org/download.cgi) 3.0-s vagy újabb verzió.

## <a name="create-an-app-configuration-instance"></a>Alkalmazáskonfigurációs példány létrehozása

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. A **Szolgáltatáskezelő** > **+Hozzáadás** lehetőséget `Beta`választva hozzáadhat egy szolgáltatásjelzőt.

    > [!div class="mx-imgBorder"]
    > ![Béta nevű szolgáltatásjelző engedélyezése](media/add-beta-feature-flag.png)

    Egyelőre hagyja `label` meg határozatlanul.

## <a name="create-a-spring-boot-app"></a>Tavaszi rendszerindítási alkalmazás létrehozása

A [tavaszi kezdőbetűvel](https://start.spring.io/) új tavaszi boot projektet hozhat létre.

1. Nyissa meg a következő címet: <https://start.spring.io/>.

1. Adja meg a következő beállításokat:

   * Hozzon létre egy **Maven** projektet **Javával**.
   * Adjon meg egy 2.0-s vagy annál nagyobb **tavaszi rendszerindítási** verziót.
   * Adja meg az alkalmazáshoz tartozó **Group** (Csoport) és **Artifact** (Összetevő) neveket.  Ez a `com.example` `demo`cikk a és a .
   * Adja hozzá a **tavaszi webes** függőséget.

1. Miután megadta az előző beállításokat, válassza **a Projekt létrehozása lehetőséget.** Amikor a rendszer kéri, töltse le a projektet a helyi számítógépre.

## <a name="add-feature-management"></a>Szolgáltatáskezelés hozzáadása

1. Miután kibontja a fájlokat a helyi rendszeren, a Spring Boot alkalmazás készen áll a szerkesztésre. Keresse meg a *pom.xml fájlt* az alkalmazás gyökérkönyvtárában.

1. Nyissa meg a *pom.xml* fájlt egy szövegszerkesztőben, `<dependencies>`és vegye fel a következőket a .:

### <a name="spring-cloud-11x"></a>Tavaszi felhő 1.1.x

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.1.2</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-feature-management-web</artifactId>
        <version>1.1.2</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    ```

### <a name="spring-cloud-12x"></a>Tavaszi felhő 1.2.x

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.2.2</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-feature-management-web</artifactId>
        <version>1.2.2</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    ```

> [!Note]
> Van egy nem webes szolgáltatáskezelő könyvtár, amely nem függ a tavaszi webtől. A különbségeket a GitHub [dokumentációjában](https://github.com/microsoft/spring-cloud-azure/tree/master/spring-cloud-azure-feature-management) találja.

## <a name="connect-to-an-app-configuration-store"></a>Csatlakozás alkalmazáskonfigurációs tárolóhoz

1. Nyissa meg `resources` az alkalmazás könyvtárát, és nyissa meg a alkalmazást. `bootstrap.properties`  Ha a fájl nem létezik, hozza létre. Adja hozzá a következő sort a fájlhoz.

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].connection-string= ${APP_CONFIGURATION_CONNECTION_STRING}
    ```

1. A config store alkalmazáskonfigurációs portálján válasszon `Access keys` az oldalsávból. Válassza az Írásvédett billentyűk lapot.

1. Adja hozzá az elsődleges kapcsolati karakterláncot `APP_CONFIGURATION_CONNECTION_STRING`környezeti változóként a változó nevével.

1. Nyissa meg a fő alkalmazás `@EnableConfigurationProperties` Java fájlt, és hozzá, hogy ezt a funkciót.

    ```java
    package com.example.demo;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.boot.context.properties.EnableConfigurationProperties;
    import org.springframework.boot.autoconfigure.SpringBootApplication;

    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class DemoApplication {

        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
    ```

1. Hozzon létre egy *MessageProperties.java* nevű új Java-fájlt az alkalmazás csomagkönyvtárában.

    ```java
    package com.example.demo;

    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.context.annotation.Configuration;

    @Configuration
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

1. Hozzon létre egy *HelloController.java* nevű új Java-fájlt az alkalmazás csomagkönyvtárában.

    ```java
    package com.example.demo;

    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;

    import com.microsoft.azure.spring.cloud.feature.manager.FeatureManager;
    import org.springframework.web.bind.annotation.GetMapping;


    @Controller
    @ConfigurationProperties("controller")
    public class HelloController {

        private FeatureManager featureManager;

        public HelloController(FeatureManager featureManager) {
            this.featureManager = featureManager;
        }

        @GetMapping("/welcome")
        public String mainWithParam(Model model) {
            model.addAttribute("Beta", featureManager.isEnabledAsync("Beta").block());
            return "welcome";
        }
    }
    ```

1. Hozzon létre egy *welcome.html* nevű új HTML-fájlt az alkalmazás sablonkönyvtárában.

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

1. Hozzon létre egy új `static` mappát nevű CSS alatt és belül egy új CSS fájl nevű *main.css*.

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

1. Állítsa össze a Spring Boot-alkalmazást a Mavennel, és futtassa.

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Nyisson meg egy böngészőablakot, `http://localhost:8080/welcome`és nyissa meg az URL-címet: .

    ![A gyorsindítási alkalmazás helyi indítása](./media/quickstarts/spring-boot-feature-flag-local-before.png)

1. Az Alkalmazáskonfigurációs portálon válassza a **Szolgáltatáskezelő**lehetőséget, és módosítsa a Béta kulcs állapotát **Be :(A) csoportban.** **Beta**

    | Kulcs | Állapot |
    |---|---|
    | Beta | Bekapcsolva |

1. Frissítse a böngészőlapot az új konfigurációs beállítások megtekintéséhez.

    ![A gyorsindítási alkalmazás helyi indítása](./media/quickstarts/spring-boot-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy új App Configuration store-t, és arra használta, hogy kezelje a tavaszi rendszerindítási webalkalmazások funkcióit a [Szolgáltatáskezelési kódtárakon](https://go.microsoft.com/fwlink/?linkid=2074664)keresztül.

* További információ a [szolgáltatáskezelésről.](./concept-feature-management.md)
* [A szolgáltatásjelzők kezelése](./manage-feature-flags.md).
* [A Spring Boot Core alkalmazásban használja a szolgáltatásjelzőket.](./use-feature-flags-spring-boot.md)
