---
title: "Az Azure Tárolószolgáltatásban Linux rugó rendszerindító webalkalmazás üzembe helyezése |} Microsoft Docs"
description: "Ez az oktatóanyag bemutatja, hogyan, ha egy rugó rendszerindító alkalmazásra, amely a Microsoft Azure Linux webes alkalmazás telepítésének a lépéseit."
services: container-service
documentationcenter: java
author: rmcmurray
manager: cfowler
editor: 
ms.assetid: 
ms.service: container-service
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 08/04/2017
ms.author: asirveda;robmcm
ms.custom: mvc
ms.openlocfilehash: fd071dc8ce568602db3d96d4bcaa5f421bd16513
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="deploy-a-spring-boot-application-on-linux-in-the-azure-container-service"></a>A rugó rendszerindító alkalmazás az Azure Tárolószolgáltatásban Linux központi telepítése

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

A  **[rugó keretrendszer]**  egy nyílt forráskódú megoldás, amely a fejlesztőket Java vállalati szintű alkalmazásokat. A beépített több népszerű projektek a platform egyik [rugó rendszerindító], amely lehetővé teszi egy egyszerűsített megközelítés önálló Java-alkalmazások létrehozása.

**[Docker]**  van nyílt forráskódú megoldások, amelyek segítségével a fejlesztők automatizálja a központi telepítési méretezés és a tárolók a futó alkalmazások kezelését.

Ez az oktatóanyag bemutatja, hogyan Docker használatával történő fejlesztéséhez és a Linux gazdagépre rugó rendszerindító alkalmazást központilag a [Azure tároló szolgáltatás (ACS)].

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban szereplő lépések végrehajtásához kell rendelkeznie a következő előfeltételek teljesülését:

* Azure-előfizetés; Ha még nem rendelkezik Azure-előfizetéssel, aktiválhatja a [MSDN-előfizetői előnyeit] vagy regisztráljon egy [ingyenes Azure-fiókot].
* A [Azure parancssori felület (CLI)].
* Egy naprakész [Java fejlesztői készlet (JDK)].
* Apache tartozó [Maven] eszköz (3-as verziójához) létrehozása.
* A [Git] ügyfél.
* A [Docker] ügyfél.

> [!NOTE]
>
> Ez az oktatóanyag virtualizálási követelményeinek, mert; virtuális gépen nem kövesse a cikkben leírt lépéseket a fizikai számítógép engedélyezett virtualizációs szolgáltatások kell használnia.
>

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>A rugó rendszerindító létrehozása webalkalmazásban Docker első lépések

A következő lépések végigvezetik hozzon létre egy egyszerű rugó rendszerindító webalkalmazást, és helyben tesztelheti a lépéseit.

1. Nyisson meg egy parancssort, és hozzon létre egy helyi könyvtárat az alkalmazás tárolására, és módosítsa a könyvtárhoz; Példa:
   ```
   md C:\SpringBoot
   cd C:\SpringBoot
   ```
   – vagy –
   ```
   md /users/robert/SpringBoot
   cd /users/robert/SpringBoot
   ```

1. Klónozott a [rugó rendszerindító a Docker bevezetés] mintaprojektet a könyvtárba, amely létrehozta; például:
   ```
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Módosítsa a könyvtárat a befejezett projekthez; Példa:
   ```
   cd gs-spring-boot-docker/complete
   ```

1. Build Maven; használatával JAR-fájlra Példa:
   ```
   mvn package
   ```

1. A webalkalmazás létrehozása után módosítsa a könyvtárat a `target` könyvtárban, ahol a JAR-fájlra is, és indítsa el a webalkalmazást; például:
   ```
   cd target
   java -jar gs-spring-boot-docker-0.1.0.jar
   ```

1. A webes alkalmazás tesztelése a azt helyileg a webböngésző segítségével. Például ha a curl érhető el, és konfigurálni a Tomcat kiszolgálón 80-as porton:
   ```
   curl http://localhost
   ```

1. A következő üzenet jelenik meg: **Hello Docker World!**

   ![Keresse meg a helyi mintaalkalmazás][SB01]

## <a name="create-an-azure-container-registry-to-use-as-a-private-docker-registry"></a>Hozzon létre egy Azure-tároló beállításjegyzék kívánja használni, mint egy titkos Docker-beállításjegyzék

A következő lépések végigvezetik Önt az Azure portál segítségével hozza létre az Azure-tároló beállításkulcs használatával.

> [!NOTE]
>
> Ha szeretné használni az Azure parancssori felület helyett az Azure-portálon, kövesse a [hozza létre az Azure CLI 2.0 használatával saját Docker tároló beállításkulcs](../../container-registry/container-registry-get-started-azure-cli.md).
>

1. Keresse meg a [Azure-portálon] , jelentkezzen be.

   Miután bejelentkezett az Azure portálon fiókjába, a lépések a [hozza létre az Azure portál használatával saját Docker tároló beállításkulcs] cikk, amely a sake a következő lépések vannak átfogalmazni célszerűségi.

1. Kattintson az menü **+ új**, majd kattintson a **tárolók**, és kattintson a **Azure tároló beállításjegyzék**.
   
   ![Hozzon létre egy új Azure-tároló beállításjegyzék][AR01]

1. Ha az adatokat az Azure-tároló beállításjegyzék sablon megjelenik, kattintson **létrehozása**. 

   ![Hozzon létre egy új Azure-tároló beállításjegyzék][AR02]

1. Ha a **létrehozás tároló beállításjegyzék** lap is megjelenik, írja be a **beállításjegyzék neve** és **erőforráscsoport**, válassza a **engedélyezése** a a **Rendszergazdai jogú felhasználó**, és kattintson a **létrehozása**.

   ![Azure-tároló beállításjegyzék-beállítások konfigurálása][AR03]

1. Miután létrejött a tároló beállításjegyzék, keresse meg a tároló beállításjegyzék, az Azure portálon, és kattintson **hívóbetűk**. Jegyezze fel a felhasználónevet és jelszót a következő lépéseket.

   ![Az Azure tároló beállításkulcsok hozzáférés][AR04]

## <a name="configure-maven-to-use-your-azure-container-registry-access-keys"></a>Maven használata az Azure tároló hozzáférés beállításkulcsok konfigurálása

1. Nyissa meg a Maven telepítés konfigurációs könyvtára, és nyissa meg a *settings.xml* fájlt egy szövegszerkesztőben.

1. Ez az oktatóanyag előző szakaszából a Azure tároló beállításjegyzék-hozzáférési beállítások hozzáadása a `<servers>` gyűjtemény a *settings.xml* fájl; például:

   ```xml
   <settings>
       <pluginGroups>
           <pluginGroup>com.spotify</pluginGroup>
       </pluginGroups>
   </settings>
   <servers>
      <server>
         <id>wingtiptoysregistry</id>
         <username>wingtiptoysregistry</username>
         <password>AbCdEfGhIjKlMnOpQrStUvWxYz</password>
         <configuration>
            <email>foo@foo.bar</email>
         </configuration>
      </server>
   </servers>
   ```

1. Keresse meg a befejezett projekt könyvtárát a rugó rendszerindító alkalmazás (például: "*C:\SpringBoot\gs-spring-boot-docker\complete*"vagy"*/users/robert/SpringBoot/gs-spring-boot-docker/complete* "), és nyissa meg a *pom.xml* fájlt egy szövegszerkesztőben.

1. Frissítés a `<properties>` gyűjtemény a *pom.xml* Ez az oktatóanyag előző szakaszából a Azure tároló beállításjegyzék bejelentkezési kiszolgáló értékű fájlt például:

   ```xml
   <properties>
      <docker.image.prefix>wingtiptoysregistry.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
   </properties>
   ```

1. Frissítés a `<plugins>` gyűjtemény a *pom.xml* fájlt úgy, hogy a `<plugin>` bejelentkezési kiszolgáló címét és a beállításjegyzék nevét tartalmazza az Azure-tároló beállításjegyzék Ez az oktatóanyag előző szakaszából. Példa:

   ```xml
   <plugin>
      <groupId>com.spotify</groupId>
      <artifactId>docker-maven-plugin</artifactId>
      <version>0.4.11</version>
      <configuration>
         <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
         <dockerDirectory>${project.basedir}</dockerDirectory>
         <resources>
            <resource>
               <targetPath>/</targetPath>
               <directory>${project.build.directory}</directory>
               <include>${project.build.finalName}.jar</include>
            </resource>
         </resources>
         <serverId>wingtiptoysregistry</serverId>
         <registryUrl>https://wingtiptoysregistry.azurecr.io</registryUrl>
      </configuration>
   </plugin>
   ```

1. Keresse meg a befejezett projekt könyvtárát rugó rendszerindító alkalmazás, és építse újra az alkalmazást, és küldje le a tároló az Azure-tároló beállításjegyzék a következő parancsot:

   ```
   mvn package docker:build -DpushImage 
   ```

> [!NOTE]
>
> A Docker-tároló leküldendő az Azure-ba, amikor is megjelenhet az alábbihoz hasonló hibaüzenetet még akkor is, abban az esetben, ha a Docker-tároló létrehozása sikerült:
>
> * `[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:0.4.11:build (default-cli) on project gs-spring-boot-docker: Exception caught: no basic auth credentials`
>
> * `[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:0.4.11:build (default-cli) on project gs-spring-boot-docker: Exception caught: Incomplete Docker registry authorization credentials. Please provide all of username, password, and email or none.`
>
> Ha ez történik, szükség lehet az Azure-fiókjával jelentkezzen be a Docker parancssorból; Példa:
>
> `docker login -u wingtiptoysregistry -p "AbCdEfGhIjKlMnOpQrStUvWxYz" wingtiptoysregistry.azurecr.io`
>
> Majd tolható ki a tárolót a parancssorból; Példa:
>
> `docker push wingtiptoysregistry.azurecr.io/gs-spring-boot-docker`
>

## <a name="create-a-web-app-on-linux-on-azure-app-service-using-your-container-image"></a>Webalkalmazás létrehozása az Azure App Service a tároló lemezkép használata Linux rendszeren

1. Keresse meg a [Azure-portálon] , jelentkezzen be.

1. Kattintson az menü **+ új**, majd kattintson **Web + mobil**, és kattintson a **webalkalmazás Linux**.
   
   ![Új webalkalmazás létrehozása az Azure-portálon][LX01]

1. Ha a **Linux webalkalmazás** lap is megjelenik, írja be a következő adatokat:

   a. Adjon meg egy egyedi nevet a **alkalmazásnév**; például: "*wingtiptoyslinux*."

   b. Válassza ki a **előfizetés** a legördülő listából.

   c. Válasszon egy meglévő **erőforráscsoport**, vagy adjon meg egy új erőforráscsoport létrehozása.

   d. Kattintson a **konfigurálása tároló** , és írja be a következő információkat:

      * Válasszon **titkos beállításjegyzék**.

      * **Kép és a nem kötelező címke**: írja be a tároló nevét, a korábbi, például: "*wingtiptoysregistry.azurecr.io/gs-spring-boot-docker:latest*"

      * **URL-címe**: Adja meg a beállításjegyzék URL-CÍMÉT, korábban; például: "*https://wingtiptoysregistry.azurecr.io*"

      * **Bejelentkezési felhasználónevének** és **jelszó**: Adja meg a bejelentkezési hitelesítő adatait a **hívóbetűk** , amelyet az előző lépésben használt.
   
   e. Miután megadta a fenti adatokat, kattintson a **OK**.

   ![A webalkalmazás-beállítások konfigurálása][LX02]

1. Kattintson a **Create** (Létrehozás) gombra.

> [!NOTE]
>
> Azure automatikusan felelteti meg beágyazott Tomcat kiszolgálót, a 80-as vagy a 8080-as szabványos portok futó Internet kérelmeket. Azonban ha konfigurálta a beágyazott Tomcat kiszolgálót futhat az egyéni portot, meg kell egy környezeti változó hozzáadása a webalkalmazáshoz, amely meghatározza a beágyazott Tomcat-kiszolgáló port. Ehhez a következőket kell tennie:
>
> 1. Keresse meg a [Azure-portálon] , jelentkezzen be.
> 
> 2. Kattintson az **alkalmazásszolgáltatások**. (Lásd az alábbi képen #1 elemére.)
>
> 3. Válassza ki a webalkalmazását a listáról. (#2 elem az alábbi képen.)
>
> 4. Kattintson a **Alkalmazásbeállítások**. (Eleme #3 az alábbi képen.)
>
> 5. A a **Alkalmazásbeállítások** területen adjon hozzá egy új környezeti változó nevű **PORT** és adja meg az egyéni portszám értékét. (#4 a eleme az alábbi képen.)
>
> 6. Kattintson a **Save** (Mentés) gombra. (#5 a eleme az alábbi képen.)
>
> ![Egyéni portszám mentése az Azure-portálon][LX03]
>

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

Azure rugó rendszerindító alkalmazások használatával kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Az Azure App Service egy rugó rendszerindító alkalmazás központi telepítése](../../app-service/app-service-deploy-spring-boot-web-app-on-azure.md)
* [A rugó rendszerindító alkalmazás az az Azure Tárolószolgáltatásban Kubernetes fürt központi telepítése](container-service-deploy-spring-boot-app-on-kubernetes.md)

Az Azure Javával való használatáról további információ: [Azure Java fejlesztői központ] és [Java-eszközök a Visual Studio Team Serviceshez].

A rugó rendszerindító Docker mintaprojektet a további részleteiért lásd: [rugó rendszerindító a Docker bevezetés].

Első lépések a saját rugó rendszerindító alkalmazásokkal kapcsolatban lásd: a **rugó Initializr** https://start.spring.io/ címen.

Első lépések egy egyszerű rugó rendszerindító alkalmazás létrehozásával kapcsolatos további információkért tekintse meg a következő https://start.spring.io/ rugó Initializr.

Az egyéni Docker lemezképek használata Azure további példákért lásd [Azure webalkalmazás Linux rendszeren egyéni Docker-lemezkép használatával].

<!-- URL List -->

[Azure parancssori felület (CLI)]: /cli/azure/overview
[Azure tároló szolgáltatás (ACS)]: https://azure.microsoft.com/services/container-service/
[Azure Java fejlesztői központ]: https://azure.microsoft.com/develop/java/
[Azure-portálon]: https://portal.azure.com/
[hozza létre az Azure portál használatával saját Docker tároló beállításkulcs]: /azure/container-registry/container-registry-get-started-portal
[Azure webalkalmazás Linux rendszeren egyéni Docker-lemezkép használatával]: /azure/app-service-web/app-service-linux-using-custom-docker-image
[Docker]: https://www.docker.com/
[ingyenes Azure-fiókot]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java fejlesztői készlet (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[Java-eszközök a Visual Studio Team Serviceshez]: https://java.visualstudio.com/
[Maven]: http://maven.apache.org/
[MSDN-előfizetői előnyeit]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[rugó rendszerindító]: http://projects.spring.io/spring-boot/
[rugó rendszerindító a Docker bevezetés]: https://github.com/spring-guides/gs-spring-boot-docker
[rugó keretrendszer]: https://spring.io/

<!-- IMG List -->

[SB01]: ./media/container-service-deploy-spring-boot-app-on-linux/SB01.png
[SB02]: ./media/container-service-deploy-spring-boot-app-on-linux/SB02.png

[AR01]: ./media/container-service-deploy-spring-boot-app-on-linux/AR01.png
[AR02]: ./media/container-service-deploy-spring-boot-app-on-linux/AR02.png
[AR03]: ./media/container-service-deploy-spring-boot-app-on-linux/AR03.png
[AR04]: ./media/container-service-deploy-spring-boot-app-on-linux/AR04.png

[LX01]: ./media/container-service-deploy-spring-boot-app-on-linux/LX01.png
[LX02]: ./media/container-service-deploy-spring-boot-app-on-linux/LX02.png
[LX03]: ./media/container-service-deploy-spring-boot-app-on-linux/LX03.png
