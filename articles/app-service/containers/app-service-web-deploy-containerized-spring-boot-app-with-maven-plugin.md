---
title: "A Maven beépülő modul használata az tárolókat webalkalmazás indexelése rugó rendszerindító alkalmazás telepítése az Azure-bA"
description: "Ismerje meg a Maven beépülő modul használata az tárolókat webalkalmazás rugó rendszerindító alkalmazás telepítése az Azure-bA."
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
ms.openlocfilehash: 0329aa9b88c7542ab3235a104a0652cd217ff872
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2017
---
# <a name="how-to-use-the-maven-plugin-for-web-app-for-containers-to-deploy-a-containerized-spring-boot-app-to-azure"></a>A Maven beépülő modul használata az tárolókat webalkalmazás indexelése rugó rendszerindító alkalmazás telepítése az Azure-bA

A [Maven beépülő modul az tárolókat webalkalmazás](https://github.com/Microsoft/azure-maven-plugins/tree/master/azure-webapp-maven-plugin) a [Apache Maven](http://maven.apache.org/) Maven-projektek az Azure App Service zökkenőmentes integrációt biztosít, és megkönnyíti a fejlesztők számára a webes alkalmazásokat telepíteni Az Azure App Service.

Ez a cikk bemutatja, hogy a Maven beépülő modul használatával az tárolókat webalkalmazás egy Docker-tároló rugó rendszerindító mintaalkalmazás telepítése az tárolókat webalkalmazásban.

> [!NOTE]
>
> A Maven beépülő modul az tárolókat webalkalmazás érhető el jelenleg által megtekinthető villámnézetként. Egyelőre csak az FTP-közzététel támogatott, bár a jövőben további funkciók tervbe van véve.
>

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban szereplő lépések végrehajtásához kell rendelkeznie a következő előfeltételek teljesülését:

* Azure-előfizetés; Ha még nem rendelkezik Azure-előfizetéssel, aktiválhatja a [MSDN-előfizetői előnyeit] vagy regisztráljon egy [ingyenes Azure-fiókot].
* A [Azure parancssori felület (CLI)].
* Egy naprakész [Java fejlesztői készlet (JDK)], 1.7 vagy újabb verziója.
* Apache tartozó [Maven] eszköz (3-as verziójához) létrehozása.
* A [Git] ügyfél.
* A [Docker] ügyfél.

> [!NOTE]
>
> Ez az oktatóanyag virtualizálási követelményeinek, mert; virtuális gépen nem kövesse a cikkben leírt lépéseket a fizikai számítógép engedélyezett virtualizációs szolgáltatások kell használnia.
>

## <a name="clone-the-sample-spring-boot-application"></a>Klónozza a mintaalkalmazást rugó rendszerindító

Ebben a szakaszban egy indexelése rugó rendszerindító alkalmazás klónozása, és helyben tesztelheti.

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

1. A [Docker rugó rendszerindítás] mintaprojektet klónozza a könyvtárba, amely létrehozta; Példa:
   ```shell
   git clone https://github.com/microsoft/gs-spring-boot-docker
   ```

1. Módosítsa a könyvtárat a befejezett projekthez; Példa:
   ```shell
   cd gs-spring-boot-docker/complete
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

1. A következő üzenet jelenik meg: **Hello Docker World**

## <a name="create-an-azure-service-principal"></a>Hozzon létre egy Azure szolgáltatás egyszerű

Ez a szakasz hozzon létre egy Azure szolgáltatás egyszerű a Maven beépülő modult használó Azure a tárolót telepítésekor.

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
   > Ez a JSON-válasz értékeinek fogja használni, a Maven beépülő modul a tároló üzembe az Azure-ba való konfigurálásakor. A `aaaaaaaa`, `uuuuuuuu`, `pppppppp`, és `tttttttt` helyőrző értékek, ebben a példában használt könnyebben ezeket az értékeket leképezi a megfelelő elemeket a Maven konfigurálásakor `settings.xml` fájl a következő szakaszban.
   >
   >

## <a name="configure-maven-to-use-your-azure-service-principal"></a>Az Azure szolgáltatás egyszerű használandó Maven konfigurálása

Ebben a szakaszban az értékeket az egyszerű Azure szolgáltatás konfigurálására használt a hitelesítési Maven fog használni, amikor a tároló telepítése az Azure-bA.

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
   `<environment>` | Határozza meg a cél Azure felhőalapú környezet, amely `AZURE` ebben a példában. (A környezetek teljes listája megtalálható a [Maven beépülő modul az tárolókat webalkalmazás] dokumentációja)

1. Mentse és zárja be a *settings.xml* fájlt.

## <a name="optional-deploy-your-local-docker-file-to-docker-hub"></a>Választható lehetőség: Fájl központi telepítése a helyi Docker Docker-hubhoz

Ha egy Docker-fiókot, létrehozhatja a Docker tároló kép helyileg és hogy Docker Hub. Ehhez használja az alábbi lépéseket.

1. Nyissa meg a `pom.xml` fájlt egy szövegszerkesztőben rugó rendszerindító alkalmazás.

1. Keresse meg a `<imageName>` gyermekeleme a `<containerSettings>` elemet.

1. Frissítés a `${docker.image.prefix}` érték és a Docker-fiók neve:
   ```xml
   <containerSettings>
      <imageName>mydockeraccountname/${project.artifactId}</imageName>
   </containerSettings>
   ```

1. Az alábbi telepítési módszerek közül választhat:

   * A tároló lemezkép helyileg fejlesztheti az Maven, és a tároló Docker-hubhoz leküldéses Docker használja:
      ```shell
      mvn clean package docker:build
      docker push
      ```

   * Ha rendelkezik a [Docker beépülő modul Maven] automatikusan létrehozhat telepítve, és a Docker-hubhoz lemezképet a tárolót a `-DpushImage` paraméter:
      ```shell
      mvn clean package docker:build -DpushImage
      ```

## <a name="optional-customize-your-pomxml-before-deploying-your-container-to-azure"></a>Választható lehetőség: Testre szabhatja a pom.xml Azure a tárolót telepítése előtt

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
         <appName>maven-linux-app-${maven.build.timestamp}</appName>
         <region>westus</region>
         <containerSettings>
            <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
         </containerSettings>
         <appSettings>
            <property>
               <name>PORT</name>
               <value>8080</value>
            </property>
         </appSettings>
      </configuration>
   </plugin>
   ```

Több érték, amely a Maven beépülő modul módosíthatja, és az ilyen elemek részletes leírását is elérhető a [Maven beépülő modul az tárolókat webalkalmazás] dokumentációját. Amely éppen említett, több érték, amelyek érdemes ebben a cikkben kiemelve:

Elem | Leírás
---|---|---
`<version>` | Verzióját adja meg a [Maven beépülő modul az tárolókat webalkalmazás]. A felsorolt verzió ellenőrizni kell a [Maven központi tárház](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-webapp-maven-plugin%22) annak érdekében, hogy a legújabb verzióját használja.
`<authentication>` | Megadja a hitelesítő adatok az Azure-hoz, amely ebben a példában tartalmaz egy `<serverId>` elem, amely tartalmazza `azure-auth`; Maven használja ezt az értéket kereséséhez az Azure szolgáltatás egyszerű értékek a a Mavenben *settings.xml* fájl, ez a cikk korábbi részében megadott.
`<resourceGroup>` | Adja meg a célként megadott erőforráscsoportja, amely `maven-plugin` ebben a példában. Az erőforráscsoport üzembe helyezése során létrejön, ha még nem létezik.
`<appName>` | A cél neve a webalkalmazás. Ebben a példában a cél neve: `maven-linux-app-${maven.build.timestamp}`, ahol a `${maven.build.timestamp}` utótagot fűz hozzá a ütközés elkerülése érdekében ebben a példában. (Az időbélyeg nem kötelező, megadhatja, hogy minden egyedi karakterláncot az alkalmazás nevére.)
`<region>` | Megadja a cél régió nevét, amely ebben a példában `westus`. (Teljes listája megtalálható a [Maven beépülő modul az tárolókat webalkalmazás] dokumentációját.)
`<appSettings>` | Meghatározza a webalkalmazás telepítése az Azure-bA használandó Maven bármely egyedi beállításait. Ebben a példában egy `<property>` elem tartalmazza-e a név-érték pár, amelyek adja meg a portot, az alkalmazás a.

> [!NOTE]
>
> Módosítsa a portszámot, ebben a példában a beállítások csak akkor szükség, ha a port módosítani az alapértelmezett.
>

## <a name="build-and-deploy-your-container-to-azure"></a>Hozza létre, és a tároló üzembe az Azure-bA

Miután a cikk a fenti szakaszokban konfigurálta a beállítások, készen áll a tároló üzembe az Azure-bA. Ehhez a következőket kell tennie:

1. A parancssort vagy terminálablakot korábban használt, építse újra a Maven használatával, ha a módosítás JAR-fájlra a *pom.xml* fájl; például:
   ```shell
   mvn clean package
   ```

1. A webalkalmazás telepítése az Azure használatával Maven; Példa:
   ```shell
   mvn azure-webapp:deploy
   ```

Maven telepíti a webalkalmazás Azure; Ha a webalkalmazás már nem létezik, a rendszer létrehozza.

> [!NOTE]
>
> Ha a régiót, amelyet meg a `<region>` eleme a *pom.xml* fájl nem rendelkezik elegendő kiszolgáló érhető el, ha a telepítés megkezdése, láthatja az alábbi példához hasonló hiba:
>
> ```bash
> [INFO] Start deploying to Web App maven-linux-app-20170804...
> [INFO] ------------------------------------------------------------------------
> [INFO] BUILD FAILURE
> [INFO] ------------------------------------------------------------------------
> [INFO] Total time: 31.059 s
> [INFO] Finished at: 2017-08-04T12:15:47-07:00
> [INFO] Final Memory: 51M/279M
> [INFO] ------------------------------------------------------------------------
> [ERROR] Failed to execute goal com.microsoft.azure:azure-webapp-maven-plugin:0.1.3:deploy (default-cli) on project gs-spring-boot-docker: null: MojoExecutionException: CloudException: OnError while emitting onNext value: retrofit2.Response.class
> ```
>
> Ha ez történik, adjon meg egy másik régióban, és futtassa újra a Maven-parancsot az alkalmazás közzétételéhez.
>
>

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

* [Maven beépülő modul az tárolókat webalkalmazás]

* [Jelentkezzen be az Azure az az Azure parancssori felület](/azure/xplat-cli-connect)

* [A Maven beépülő modul használata az tárolókat webalkalmazás rugó rendszerindító alkalmazás telepítése az Azure App Service Linux rendszeren](../app-service-web-deploy-spring-boot-app-with-maven-plugin.md)

* [Hozzon létre egy Azure szolgáltatás egyszerű Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli)

* [Maven-beállítások referenciája](https://maven.apache.org/settings.html)

* [Docker beépülő modul Maven]

<!-- URL List -->

[Azure parancssori felület (CLI)]: /cli/azure/overview
[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/
[Azure-portálon]: https://portal.azure.com/
[Docker]: https://www.docker.com/
[Docker beépülő modul Maven]: https://github.com/spotify/docker-maven-plugin
[ingyenes Azure-fiókot]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java Developer Kit (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/
[Maven]: http://maven.apache.org/
[MSDN-előfizetői előnyeit]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Boot on Docker Getting Started]: https://github.com/spring-guides/gs-spring-boot-docker
[Spring Framework]: https://spring.io/
[Maven beépülő modul az tárolókat webalkalmazás]: https://github.com/Microsoft/azure-maven-plugins/tree/master/azure-webapp-maven-plugin

<!-- IMG List -->

[AP01]: ./media/app-service-web-deploy-containerized-spring-boot-app-with-maven-plugin/AP01.png
[AP02]: ./media/app-service-web-deploy-containerized-spring-boot-app-with-maven-plugin/AP02.png
