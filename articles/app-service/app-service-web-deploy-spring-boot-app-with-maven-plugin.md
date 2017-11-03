---
title: "A rugó rendszerindító alkalmazás telepítése az Azure Azure Web Apps a Maven beépülő modul használata"
description: "Útmutató az Azure Web Apps Maven beépülő modul segítségével rugó rendszerindító alkalmazás telepítése az Azure-bA."
services: app-service\web
documentationcenter: java
author: rmcmurray
manager: cfowler
editor: 
ms.assetid: 
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 08/07/2017
ms.author: robmcm;kevinzha
ms.openlocfilehash: f22089349e149d8e71df88d5c173869d2a5abad7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-the-maven-plugin-for-azure-web-apps-to-deploy-a-spring-boot-app-to-azure"></a>A rugó rendszerindító alkalmazás telepítése az Azure Azure Web Apps a Maven beépülő modul használata

A [Maven beépülő modul Azure Web Apps](https://github.com/Microsoft/azure-maven-plugins/tree/master/azure-webapp-maven-plugin) a [Apache Maven](http://maven.apache.org/) Maven-projektek az Azure App Service zökkenőmentes integrációt biztosít, és megkönnyíti a fejlesztők számára telepíthet webalkalmazásokat az Azure App Service szolgáltatásban.

Ez a cikk bemutatja, hogy az Azure App Service szolgáltatások rugó rendszerindító mintaalkalmazás telepítése Azure Web Apps a Maven beépülő modul használatával.

> [!NOTE]
>
> Az Azure Web Apps Maven beépülő modul jelenleg tartozik által megtekinthető villámnézetként. Egyelőre csak az FTP-közzététel támogatott, bár a jövőben további funkciók tervbe van véve.
>

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban szereplő lépések végrehajtásához kell rendelkeznie a következő előfeltételek teljesülését:

* Azure-előfizetés; Ha még nem rendelkezik Azure-előfizetéssel, aktiválhatja a [MSDN-előfizetői előnyeit] vagy regisztráljon egy [ingyenes Azure-fiókot].
* A [Azure parancssori felület (CLI)].
* Egy naprakész [Java fejlesztői készlet (JDK)], 1.7 vagy újabb verziója.
* Apache tartozó [Maven] eszköz (3-as verziójához) létrehozása.
* A [Git] ügyfél.

## <a name="clone-the-sample-spring-boot-web-app"></a>A rugó rendszerindító webes mintaalkalmazás klónozása

Ebben a szakaszban egy befejezett rugó rendszerindító alkalmazás klónozása, és helyben tesztelheti.

1. Nyisson meg egy parancssort vagy terminálablakot, és hozzon létre egy helyi könyvtárat a rugó rendszerindító alkalmazás tárolására, és módosítsa a könyvtárhoz; Példa:
   ```shell
   md C:\SpringBoot
   cd C:\SpringBoot
   ```
   – vagy –
   ```shell
   md /users/robert/SpringBoot
   cd /users/robert/SpringBoot
   ```

1. Klónozott a [rugó rendszerindító bevezetés] mintaprojektet a könyvtárba, amely létrehozta; például:
   ```shell
   git clone https://github.com/microsoft/gs-spring-boot
   ```

1. Módosítsa a könyvtárat a befejezett projekthez; Példa:
   ```shell
   cd gs-spring-boot/complete
   ```

1. Build Maven; használatával JAR-fájlra Példa:
   ```shell
   mvn clean package
   ```

1. Ha a webalkalmazás létrejött, indítsa el a webalkalmazást, Maven; használatával Példa:
   ```shell
   mvn spring-boot:run
   ```

1. A webes alkalmazás tesztelése a azt helyileg a webböngésző segítségével. Használhatja például a következő parancsot, ha a curl érhető el:
   ```shell
   curl http://localhost:8080
   ```

1. A következő üzenet jelenik meg: **hónap rugó rendszerindításból!**

## <a name="create-an-azure-service-principal"></a>Hozzon létre egy Azure szolgáltatás egyszerű

Ez a szakasz hozzon létre egy Azure szolgáltatás egyszerű, amely a Maven beépülő modul használja, amikor a webalkalmazás telepítése az Azure-bA.

1. Nyisson meg egy parancssort.

1. Jelentkezzen be az Azure-fiókot az Azure parancssori felület használatával:
   ```shell
   az login
   ```
   Kövesse az utasításokat a bejelentkezési folyamat befejezéséhez.

1. Hozzon létre egy Azure-szolgáltatás egyszerű:
   ```shell
   az ad sp create-for-rbac --name "uuuuuuuu" --password "pppppppp"
   ```
   Ha `uuuuuuuu` a felhasználónév és `pppppppp` pedig a jelszót a szolgáltatás rendszerbiztonsági tag.

1. Azure JSON válaszol, a következőhöz hasonló: a következő példa:
   ```json
   {
      "appId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "displayName": "uuuuuuuu",
      "name": "http://uuuuuuuu",
      "password": "pppppppp",
      "tenant": "tttttttt-tttt-tttt-tttt-tttttttttttt"
   }
   ```

   > [!NOTE]
   >
   > Ez a JSON-válasz értékeinek fogja használni, a Maven beépülő modul a webalkalmazás telepítése az Azure-ba való konfigurálásakor. A `aaaaaaaa`, `uuuuuuuu`, `pppppppp`, és `tttttttt` helyőrző értékek, ebben a példában használt könnyebben ezeket az értékeket leképezi a megfelelő elemeket a Maven konfigurálásakor `settings.xml` fájl a következő szakaszban.
   >
   >

## <a name="configure-maven-to-use-your-azure-service-principal"></a>Az Azure szolgáltatás egyszerű használandó Maven konfigurálása

Ebben a szakaszban az értékeket az egyszerű Azure szolgáltatás konfigurálására használt Maven használó a webalkalmazás Azure történő telepítéskor a hitelesítés.

1. Nyissa meg a Maven `settings.xml` fájlt egy szövegszerkesztőben; lehet, hogy a fájl olyan elérési útja, például az alábbi példákat:
   * `/etc/maven/settings.xml`
   * `%ProgramFiles%\apache-maven\3.5.0\conf\settings.xml`
   * `$HOME/.m2/settings.xml`

1. Ez az oktatóanyag előző szakaszából a Azure szolgáltatás egyszerű beállítások hozzáadása a `<servers>` gyűjtemény a *settings.xml* fájl; például:

   ```xml
   <servers>
      <server>
        <id>azure-auth</id>
         <configuration>
            <client>aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa</client>
            <tenant>tttttttt-tttt-tttt-tttt-tttttttttttt</tenant>
            <key>pppppppp</key>
            <environment>AZURE</environment>
         </configuration>
      </server>
   </servers>
   ```
   Az elemek magyarázata:
   Elem | Leírás
   ---|---|---
   `<id>` | Adja meg egy egyedi nevet, amely Maven használja a webalkalmazás az Azure-ba való telepítésekor a biztonsági beállítások kereséséhez.
   `<client>` | Tartalmazza a `appId` a szolgáltatás egyszerű közötti értéket.
   `<tenant>` | Tartalmazza a `tenant` a szolgáltatás egyszerű közötti értéket.
   `<key>` | Tartalmazza a `password` a szolgáltatás egyszerű közötti értéket.
   `<environment>` | Határozza meg a cél Azure felhőalapú környezet, amely `AZURE` ebben a példában. (A környezetek teljes listája megtalálható a [Maven beépülő modul Azure Web Apps] dokumentációja)

1. Mentse és zárja be a *settings.xml* fájlt.

## <a name="optional-customize-your-pomxml-before-deploying-your-web-app-to-azure"></a>Választható lehetőség: Testre szabhatja a pom.xml előtt a webalkalmazás telepítése az Azure-bA

Nyissa meg a `pom.xml` fájlt egy szövegszerkesztőben rugó rendszerindító alkalmazás, és keresse meg a `<plugin>` elem `azure-webapp-maven-plugin`. Ez az elem az alábbihoz kell hasonlítania:

   ```xml
   <plugin>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-webapp-maven-plugin</artifactId>
      <version>0.1.3</version>
      <configuration>
         <authentication>
            <serverId>azure-auth</serverId>
         </authentication>
         <resourceGroup>maven-plugin</resourceGroup>
         <appName>maven-web-app-${maven.build.timestamp}</appName>
         <region>westus</region>
         <javaVersion>1.8</javaVersion>
         <deploymentType>ftp</deploymentType>
         <resources>
            <resource>
               <directory>${project.basedir}/target</directory>
               <targetPath>/</targetPath>
               <includes>
                  <include>*.jar</include>
               </includes>
            </resource>
            <resource>
               <directory>${project.basedir}</directory>
               <targetPath>/</targetPath>
               <includes>
                  <include>web.config</include>
               </includes>
            </resource>
         </resources>
      </configuration>
   </plugin>
   ```

Több érték, amely a Maven beépülő modul módosíthatja, és az ilyen elemek részletes leírását is elérhető a [Maven beépülő modul Azure Web Apps] dokumentációját. Amely éppen említett, több érték, amelyek érdemes ebben a cikkben kiemelve:

Elem | Leírás
---|---|---
`<version>` | Verzióját adja meg a [Maven beépülő modul Azure Web Apps]. A felsorolt verzió ellenőrizni kell a [Maven központi tárház](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-webapp-maven-plugin%22) annak érdekében, hogy a legújabb verzióját használja.
`<authentication>` | Megadja a hitelesítő adatok az Azure-hoz, amely ebben a példában tartalmaz egy `<serverId>` elem, amely tartalmazza `azure-auth`; Maven használja ezt az értéket kereséséhez az Azure szolgáltatás egyszerű értékek a a Mavenben *settings.xml* fájl, ez a cikk korábbi részében megadott.
`<resourceGroup>` | Adja meg a célként megadott erőforráscsoportja, amely `maven-plugin` ebben a példában. Az erőforráscsoport a telepítés során jön létre, ha még nem létezik.
`<appName>` | A cél neve a webalkalmazás. Ebben a példában a cél neve: `maven-web-app-${maven.build.timestamp}`, ahol a `${maven.build.timestamp}` utótagot fűz hozzá a ütközés elkerülése érdekében ebben a példában. (Az időbélyeg nem kötelező, megadhatja, hogy minden egyedi karakterláncot az alkalmazás nevére.)
`<region>` | Megadja a cél régió nevét, amely ebben a példában `westus`. (Teljes listája megtalálható a [Maven beépülő modul Azure Web Apps] dokumentációját.)
`<javaVersion>` | Adja meg a webalkalmazás a Java futásidejű verzióját. (Teljes listája megtalálható a [Maven beépülő modul Azure Web Apps] dokumentációját.)
`<deploymentType>` | Adja meg a webalkalmazás központi telepítési típus. Egyelőre csak `ftp` támogatott, bár a más központi telepítési típusok a fejlesztési támogatja.
`<resources>` | Megadja az erőforrások és a célként megadott célhelyekre Maven használja, ha a webalkalmazás telepítése az Azure-bA. Ebben a példában két `<resource>` elemek adja meg, hogy a Maven helyezik üzembe a JAR-fájlra a webalkalmazás és a *web.config* fájl a rugó rendszerindító projektből.

## <a name="build-and-deploy-your-web-app-to-azure"></a>Hozza létre, és a webalkalmazás telepítése az Azure-bA

Miután ez a cikk a fenti szakaszokban konfigurálta a beállítások, készen áll a webalkalmazás telepítése az Azure-bA. Ehhez a következőket kell tennie:

1. A parancssort vagy terminálablakot korábban használt, építse újra a Maven használatával, ha a módosítás JAR-fájlra a *pom.xml* fájl; például:
   ```shell
   mvn clean package
   ```

1. A webalkalmazás telepítése az Azure használatával Maven; Példa:
   ```shell
   mvn azure-webapp:deploy
   ```

Maven telepíti a webalkalmazás Azure; Ha a webalkalmazás már nem létezik, a rendszer létrehozza.

Ha a webes van telepítve, akkor fog tudni segítségével kezeli a [Azure-portálon].

* A webalkalmazás megjelenik **alkalmazásszolgáltatások**:

   ![A webes alkalmazás szerepel az Azure-portálon App Service szolgáltatások][AP01]

* A webalkalmazás URL-címe megjelenik, és a **áttekintése** webalkalmazáshoz:

   ![A webalkalmazás URL-Címének meghatározása][AP02]

<!--
##  OPTIONAL: Configure the embedded Tomcat server to run on a different port

The embedded Tomcat server in the sample Spring Boot application is configured to run on port 8080 by default. However, if you want to run the embedded Tomcat server to run on a different port, such as port 80 for local testing, you can configure the port by using the following steps.

1. Go to the *resources* directory (or create the directory if it does not exist); for example:
   ```shell
   cd src/main/resources
   ```

1. Open the *application.yml* file in a text editor if it exists, or create a new YAML file if it does not exist.

1. Modify the **server** setting so that the server runs on port 80; for example:
   ```yaml
   server:
      port: 80
   ```

1. Save and close the *application.yml* file.
-->

## <a name="next-steps"></a>Következő lépések

A cikkben említett különböző technológiákkal kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Maven beépülő modul Azure Web Apps]

* [Jelentkezzen be az Azure az az Azure parancssori felület](/azure/xplat-cli-connect)

* [Az Azure Web Apps Maven beépülő modul segítségével indexelése rugó rendszerindító alkalmazás telepítése az Azure-bA](containers/app-service-web-deploy-containerized-spring-boot-app-with-maven-plugin.md)

* [Hozzon létre egy Azure szolgáltatás egyszerű Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli)

* [Maven-beállítások referenciája](https://maven.apache.org/settings.html)

<!-- URL List -->

[Azure parancssori felület (CLI)]: /cli/azure/overview
[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/
[Azure-portálon]: https://portal.azure.com/
[ingyenes Azure-fiókot]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java Developer Kit (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/
[Maven]: http://maven.apache.org/
[MSDN-előfizetői előnyeit]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[rugó rendszerindító bevezetés]: https://github.com/microsoft/gs-spring-boot
[Spring Framework]: https://spring.io/
[Maven beépülő modul Azure Web Apps]: https://github.com/Microsoft/azure-maven-plugins/tree/master/azure-webapp-maven-plugin

<!-- IMG List -->

[AP01]: ./media/app-service-web-deploy-spring-boot-app-with-maven-plugin/AP01.png
[AP02]: ./media/app-service-web-deploy-spring-boot-app-with-maven-plugin/AP02.png
