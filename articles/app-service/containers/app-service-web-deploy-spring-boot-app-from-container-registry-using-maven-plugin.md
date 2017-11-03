---
title: "Az Azure Web Apps Maven beépülő modul használata Azure tároló beállításjegyzék rugó rendszerindító alkalmazás telepítése az Azure App Service"
description: "Ez az oktatóanyag részletesen ismerteti, ha egy Azure-tároló beállításjegyzék rugó rendszerindító alkalmazás telepítési Azure az Azure App Service egy Maven beépülő modul használatával."
services: 
documentationcenter: java
author: rmcmurray
manager: cfowler
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 08/07/2017
ms.author: robmcm;kevinzha
ms.openlocfilehash: b087003b3a1e236e4a306678904107b8bf99395e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-the-maven-plugin-for-azure-web-apps-to-deploy-a-spring-boot-app-in-azure-container-registry-to-azure-app-service"></a>Az Azure Web Apps Maven beépülő modul használata Azure tároló beállításjegyzék rugó rendszerindító alkalmazás telepítése az Azure App Service

A  **[rugó keretrendszer]**  egy népszerű nyílt forráskódú keretrendszer, amely a fejlesztőket Java webes, mobil és API-alkalmazások létrehozása. Ez az oktatóanyag használja egy mintaalkalmazást használatával létrehozott [rugó rendszerindító], rugó használatával történő gyors használatbavétel a egyezmény adatvezérelt megközelítést.

Ez a cikk bemutatja, hogyan Azure tároló beállításjegyzék rugó rendszerindító mintaalkalmazás telepítése, és az Azure Web Apps Maven beépülő modul segítségével az Azure App Service alkalmazás telepítése.

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
* A [Docker] ügyfél.

> [!NOTE]
>
> Ez az oktatóanyag virtualizálási követelményeinek, mert; virtuális gépen nem kövesse a cikkben leírt lépéseket a fizikai számítógép engedélyezett virtualizációs szolgáltatások kell használnia.
>

## <a name="clone-the-sample-spring-boot-on-docker-web-app"></a>A minta rugó rendszerindító Docker webalkalmazásban klónozása

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

1. Klónozott a [rugó rendszerindító a Docker bevezetés] mintaprojektet a könyvtárba, amely létrehozta; például:
   ```shell
   git clone -b private-registry https://github.com/Microsoft/gs-spring-boot-docker
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

   ![Keresse meg a helyi mintaalkalmazás][SB01]

## <a name="create-an-azure-service-principal"></a>Hozzon létre egy Azure szolgáltatás egyszerű

Ez a szakasz hozzon létre egy Azure szolgáltatás egyszerű a Maven beépülő modult használó Azure a tárolót telepítésekor.

1. Nyisson meg egy parancssort.

1. Jelentkezzen be az Azure-fiókot az Azure parancssori felület használatával:
   ```azurecli
   az login
   ```
   Kövesse az utasításokat a bejelentkezési folyamat befejezéséhez.

1. Hozzon létre egy Azure-szolgáltatás egyszerű:
   ```azurecli
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

## <a name="create-an-azure-container-registry-using-the-azure-cli"></a>Hozzon létre egy Azure-tároló beállításjegyzék, az Azure parancssori felület használatával

1. Nyisson meg egy parancssort.

1. Jelentkezzen be az Azure-fiókjával:
   ```azurecli
   az login
   ```

1. Ez a cikk hozzon létre egy erőforráscsoportot az Azure-erőforrások fogja használni:
   ```azurecli
   az group create --name=wingtiptoysresources --location=westus
   ```
   Cserélje le `wingtiptoysresources` ebben a példában az erőforráscsoport számára egyedi névvel.

1. Hozzon létre egy saját Azure-tárolót beállításjegyzék az erőforráscsoport rugó rendszerindító alkalmazás: 
   ```azurecli
   az acr create --admin-enabled --resource-group wingtiptoysresources --location westus --name wingtiptoysregistry --sku Basic
   ```
   Cserélje le `wingtiptoysregistry` ebben a példában a tároló beállításjegyzék egyedi nevére.

1. Lekéri a tároló beállításjegyzék jelszavát:
   ```azurecli
   az acr credential show --name wingtiptoysregistry --query passwords[0]
   ```
   Azure válaszol, és a jelszavát; Példa:
   ```json
   {
      "name": "password",
      "value": "xxxxxxxxxx"
   }
   ```

## <a name="add-your-azure-container-registry-and-azure-service-principal-to-your-maven-settings"></a>Adja hozzá az Azure-tárolót beállításjegyzék és Azure egyszerű szolgáltatásnév a Maven-beállítások

1. Nyissa meg a Maven `settings.xml` fájlt egy szövegszerkesztőben; lehet, hogy a fájl olyan elérési útja, például az alábbi példákat:
   * `/etc/maven/settings.xml`
   * `%ProgramFiles%\apache-maven\3.5.0\conf\settings.xml`
   * `$HOME/.m2/settings.xml`

1. Az ebben a cikkben előző szakaszából a Azure tároló beállításjegyzék-hozzáférési beállítások hozzáadása a `<servers>` gyűjtemény a *settings.xml* fájl; például:

   ```xml
   <servers>
      <server>
         <id>wingtiptoysregistry</id>
         <username>wingtiptoysregistry</username>
         <password>xxxxxxxxxx</password>
      </server>
   </servers>
   ```
   Az elemek magyarázata:
   Elem | Leírás
   ---|---|---
   `<id>` | A saját Azure-tárolót beállításjegyzék nevét tartalmazza.
   `<username>` | A saját Azure-tárolót beállításjegyzék nevét tartalmazza.
   `<password>` | A jelszót, ez a cikk az előző szakaszban lekért tartalmaz.

1. Ebben a cikkben egy korábbi szakaszában az Azure szolgáltatás egyszerű beállítás hozzáadása a `<servers>` gyűjtemény a *settings.xml* fájl; például:

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

## <a name="build-your-docker-container-image-and-push-it-to-your-azure-container-registry"></a>A Docker-tároló lemezképet létre, és hogy az Azure-tárolót beállításjegyzék

1. Keresse meg a befejezett projekt könyvtárát a rugó rendszerindító alkalmazás (pl. "*C:\SpringBoot\gs-spring-boot-docker\complete*"vagy"*/users/robert/SpringBoot/gs-spring-boot-docker/complete*"), és nyissa meg a *pom.xml* szöveggel fájl szerkesztő.

1. Frissítés a `<properties>` gyűjtemény a *pom.xml* Ez az oktatóanyag előző szakaszából a Azure tároló beállításjegyzék bejelentkezési kiszolgáló értékű fájlt például:

   ```xml
   <properties>
      <azure.containerRegistry>wingtiptoysregistry</azure.containerRegistry>
      <docker.image.prefix>${azure.containerRegistry}.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
      <maven.build.timestamp.format>yyyyMMddHHmmssSSS</maven.build.timestamp.format>
   </properties>
   ```
   Az elemek magyarázata:
   Elem | Leírás
   ---|---|---
   `<azure.containerRegistry>` | Megadja a saját Azure-tárolót beállításjegyzék nevét.
   `<docker.image.prefix>` | Meghatározza a URL-címet a saját Azure-tárolót beállításjegyzékről, hozzáfűzésével származtatott ". azurecr.io" a személyes tárolót beállításjegyzék nevét.

1. Ellenőrizze, hogy `<plugin>` a Docker beépülő modul a *pom.xml* fájl ebben az oktatóanyagban a bejelentkezési cím és a beállításjegyzék kiszolgálónév az előző lépésben megfelelő tulajdonságait tartalmazza. Példa:

   ```xml
   <plugin>
      <groupId>com.spotify</groupId>
      <artifactId>docker-maven-plugin</artifactId>
      <version>0.4.11</version>
      <configuration>
         <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
         <registryUrl>https://${docker.image.prefix}</registryUrl>
         <serverId>${azure.containerRegistry}</serverId>
         <dockerDirectory>src/main/docker</dockerDirectory>
         <resources>
            <resource>
               <targetPath>/</targetPath>
               <directory>${project.build.directory}</directory>
               <include>${project.build.finalName}.jar</include>
            </resource>
         </resources>
      </configuration>
   </plugin>
   ```
   Az elemek magyarázata:
   Elem | Leírás
   ---|---|---
   `<serverId>` | Adja meg azt a tulajdonságot, mely tartalmazza a saját Azure-tárolót beállításjegyzék nevét.
   `<registryUrl>` | Megadja azt a tulajdonságot, amely tartalmazza a saját Azure-tárolót beállításjegyzék URL-CÍMÉT.

1. Keresse meg a befejezett projekt könyvtárát rugó rendszerindító alkalmazás, és építse újra az alkalmazást, és küldje le a tároló az Azure-tárolót beállításjegyzék a következő parancsot:

   ```
   mvn package docker:build -DpushImage 
   ```

1. Választható lehetőség: Keresse meg a [Azure-portálon] , és ellenőrizze, hogy van-e Docker-tároló kép nevű **gs-rugó-rendszerindítás – docker** a tároló beállításjegyzékben.

   ![Ellenőrizze a tároló az Azure-portálon][CR01]

## <a name="customize-your-pomxml-then-build-and-deploy-your-container-to-azure"></a>Testre szabhatja a pom.xml majd build és a tároló üzembe az Azure-bA

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
         <resourceGroup>wingtiptoysresources</resourceGroup>
         <appName>maven-linux-app-${maven.build.timestamp}</appName>
         <region>westus</region>
         <containerSettings>
            <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
            <registryUrl>https://${docker.image.prefix}</registryUrl>
            <serverId>${azure.containerRegistry}</serverId>
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

Több érték, amely a Maven beépülő modul módosíthatja, és az ilyen elemek részletes leírását is elérhető a [Maven beépülő modul Azure Web Apps] dokumentációját. Amely éppen említett, több érték, amelyek érdemes ebben a cikkben kiemelve:

Elem | Leírás
---|---|---
`<version>` | Verzióját adja meg a [Maven beépülő modul Azure Web Apps]. A felsorolt verzió ellenőrizni kell a [Maven központi tárház](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-webapp-maven-plugin%22) annak érdekében, hogy a legújabb verzióját használja.
`<authentication>` | Megadja a hitelesítő adatok az Azure-hoz, amely ebben a példában tartalmaz egy `<serverId>` elem, amely tartalmazza `azure-auth`; Maven használja ezt az értéket kereséséhez az Azure szolgáltatás egyszerű értékek a a Mavenben *settings.xml* fájl, ez a cikk korábbi részében megadott.
`<resourceGroup>` | Adja meg a célként megadott erőforráscsoportja, amely `wingtiptoysresources` ebben a példában. Az erőforráscsoport üzembe helyezése során létrejön, ha még nem létezik.
`<appName>` | A cél neve a webalkalmazás. Ebben a példában a cél neve: `maven-linux-app-${maven.build.timestamp}`, ahol a `${maven.build.timestamp}` utótagot fűz hozzá a ütközés elkerülése érdekében ebben a példában. (Az időbélyeg nem kötelező, megadhatja, hogy minden egyedi karakterláncot az alkalmazás nevére.)
`<region>` | Megadja a cél régió nevét, amely ebben a példában `westus`. (Teljes listája megtalálható a [Maven beépülő modul Azure Web Apps] dokumentációját.)
`<containerSettings>` | A tulajdonságokat tartalmazó nevét és a tároló URL-CÍMÉT adja meg.
`<appSettings>` | Meghatározza a webalkalmazás telepítése az Azure-bA használandó Maven bármely egyedi beállításait. Ebben a példában egy `<property>` elem tartalmazza-e a név-érték pár, amelyek adja meg a portot, az alkalmazás a.

> [!NOTE]
>
> Módosítsa a portszámot, ebben a példában a beállítások csak akkor szükség, ha a port módosítani az alapértelmezett.
>

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
> ```
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

## <a name="next-steps"></a>Következő lépések

A cikkben említett különböző technológiákkal kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Maven beépülő modul Azure Web Apps]

* [Jelentkezzen be az Azure az az Azure parancssori felület](/azure/xplat-cli-connect)

* [Hozzon létre egy Azure szolgáltatás egyszerű Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli)

* [Maven-beállítások referenciája](https://maven.apache.org/settings.html)

* [Docker Maven beépülő modul]

<!-- URL List -->

[Azure parancssori felület (CLI)]: /cli/azure/overview
[Azure Container Service (ACS)]: https://azure.microsoft.com/services/container-service/
[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/
[Azure-portálon]: https://portal.azure.com/
[Maven beépülő modul Azure Web Apps]: https://github.com/Microsoft/azure-maven-plugins/tree/master/azure-webapp-maven-plugin
[Create a private Docker container registry using the Azure portal]: /azure/container-registry/container-registry-get-started-portal
[Using a custom Docker image for Azure Web App on Linux]: tutorial-custom-docker-image.md
[Docker]: https://www.docker.com/
[Docker Maven beépülő modul]: https://github.com/spotify/docker-maven-plugin
[ingyenes Azure-fiókot]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java Developer Kit (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/
[Maven]: http://maven.apache.org/
[MSDN-előfizetői előnyeit]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[rugó rendszerindító]: http://projects.spring.io/spring-boot/
[rugó rendszerindító a Docker bevezetés]: https://github.com/spring-guides/gs-spring-boot-docker
[rugó keretrendszer]: https://spring.io/

<!-- IMG List -->

[SB01]: ./media/app-service-web-deploy-spring-boot-app-from-container-registry-using-maven-plugin/SB01.png
[CR01]: ./media/app-service-web-deploy-spring-boot-app-from-container-registry-using-maven-plugin/CR01.png
[AP01]: ./media/app-service-web-deploy-spring-boot-app-from-container-registry-using-maven-plugin/AP01.png
[AP02]: ./media/app-service-web-deploy-spring-boot-app-from-container-registry-using-maven-plugin/AP02.png
