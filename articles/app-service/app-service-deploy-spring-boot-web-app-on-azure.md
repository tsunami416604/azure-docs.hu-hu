---
title: "Az Azure App Service rugó rendszerindító alkalmazást központilag |} Microsoft Docs"
description: "Ez az oktatóanyag ismerteti a lépéseit fejlesztők számára, hogy a rendszerindító bevezetés rugó webalkalmazás telepítése az Azure App Service szolgáltatásban."
services: app-service\web
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
ms.date: 08/04/2017
ms.author: asirveda;robmcm
ms.openlocfilehash: 8776142d5452bf5057990702c89aa1a541382ffc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-a-spring-boot-application-to-the-azure-app-service"></a>Spring Boot-alkalmazás üzembe helyezése az Azure App Service-ben

A  **[rugó keretrendszer]**  nyílt forráskódú megoldás, amely segít a vállalati szintű alkalmazásokat Java fejlesztői, és a további népszerű platformra épülő projektek egyik [ Rendszerindító érintkező], amely lehetővé teszi egy egyszerűsített megközelítés önálló Java-alkalmazások létrehozása.

Ez az oktatóanyag bemutatja, ha a rendszerindító bevezetés rugó webes mintaalkalmazás létrehozását és telepítését a [Azure App Service].

### <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban szereplő lépések végrehajtásához kell rendelkeznie a következő:

* Azure-előfizetés; Ha még nem rendelkezik Azure-előfizetéssel, aktiválhatja a [MSDN-előfizetői előnyeit] vagy regisztráljon egy [ingyenes Azure-fiókot].
* Egy naprakész [Java fejlesztői készlet (JDK)].
* Apache tartozó [Maven] eszköz (3-as verziójához) létrehozása.
* A [Git] ügyfél.

## <a name="create-the-spring-boot-getting-started-web-app"></a>A rugó rendszerindító első lépések a webalkalmazás létrehozása

A következő lépésekkel haladhat végig hozzon létre egy egyszerű rugó rendszerindító webalkalmazást, és helyben tesztelheti a lépéseit.

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

1. Klónozott a [rugó rendszerindító bevezetés] mintaprojektet a könyvtárba, újonnan létrehozott; például:
   ```
   git clone https://github.com/spring-guides/gs-spring-boot.git
   ```

1. Módosítsa a könyvtárat a befejezett projekthez; Példa:
   ```
   cd gs-spring-boot
   cd complete
   ```

1. Build Maven; használatával JAR-fájlra Példa:
   ```
   mvn package
   ```

1. A webalkalmazás létrehozása után módosítsa a könyvtárat a JAR-fájlra, és indítsa el a webalkalmazást; Példa:
   ```
   cd target
   java -jar gs-spring-boot-0.1.0.jar
   ```

1. A webes alkalmazás tesztelése a webböngésző segítségével 8080 tallózással, vagy használja a szintaxist, az alábbi példához hasonló, ha a curl érhető el:
   ```
   curl http://localhost:8080
   ```

1. A következő üzenet jelenik meg: **hónap rugó rendszerindításból!**

   ![Keresse meg a mintaalkalmazás][SB01]

## <a name="create-an-azure-web-app-for-use-with-java"></a>Java hozzon létre egy Azure webalkalmazás számára

A következő lépésekkel haladhat végig a lépéseket az Azure-webalkalmazás létrehozása, konfigurálja a szükséges beállításokat, Java és az FTP-hitelesítő adatok beállítása.

1. Keresse meg a [Azure-portálon] , és jelentkezzen be.

1. Miután jelentkezett, a fiókot az Azure portálon, kattintson az menü **alkalmazásszolgáltatások**:
   
   ![Azure Portal][AZ01]

1. Ha a **alkalmazásszolgáltatások** lap is megjelenik, kattintson a **+ Hozzáadás** egy új App Service létrehozása.

   ![Az App Service létrehozása][AZ02]

1. Amikor megjelenik a webes alkalmazás sablonok listáján, a Microsoft alapszintű webalkalmazást a hivatkozásra kattintva.

   ![Webes alkalmazás sablonok][AZ03]

1. Ha az adatokat a Web App sablon megjelenik, kattintson **létrehozása**.

   ![Webalkalmazás létrehozása][AZ04]

1. Adjon egyedi nevet a webalkalmazás, és adja meg az esetleges egyéb beállításokat, majd **létrehozása**.

   ![A webalkalmazás-beállítások létrehozása][AZ05]

1. A webalkalmazás létrehozása után kattintson az menü **alkalmazásszolgáltatások**, majd kattintson az újonnan létrehozott webalkalmazás:

   ![Lista webalkalmazások][AZ06]

1. Amikor megjelenik a webalkalmazás, adja meg a Java-verziót a következő lépések segítségével:

   a. Kattintson a **Alkalmazásbeállítások** menüpont.

   b. Válasszon **Java 8** a Java-verzió.

   c. Válasszon **legújabb** a kisebb Java-verzió.

   d. Válasszon **legújabb Tomcat 8.5** a webes tároló. (Ez a tároló nem ténylegesen használja; Azure fogja használni a rugó rendszerindító alkalmazás a tárolót.)

   e. Kattintson a **Save** (Mentés) gombra.

   ![Alkalmazásbeállítások][AZ07]

1. Adja meg az FTP telepítési hitelesítő adatokat az alábbi lépéseket követve:

   a. Kattintson a **üzembe helyezési hitelesítő adatok** menüpont.

   b. Adja meg a felhasználónevét és jelszavát.

   c. Kattintson a **Save** (Mentés) gombra.

   ![Adja meg az üzembe helyezési hitelesítő adatok][AZ08]

1. Az FTP-kiszolgáló kapcsolati adatainak beolvasása a következő lépések segítségével:

   a. Kattintson a **üzembe helyezési hitelesítő adatok** menüpont.

   b. Másolja a teljes FTP-felhasználónév és az URL-címet, és mentse őket az oktatóanyag következő szakasza.

   ![FTP URL-CÍMEK és a hitelesítő adatok][AZ09]

## <a name="deploy-your-spring-boot-web-app-to-azure"></a>A rugó rendszerindító webalkalmazás telepítése az Azure-bA

A következő lépésekkel haladhat végig a lépéseket a rugó rendszerindító webalkalmazás telepítése az Azure.

1. Nyisson meg egy szövegszerkesztőt, például a Jegyzettömbben Windows és a következő szöveg beillesztése egy új dokumentumot, majd mentse a fájlt *web.config*:
   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <configuration>
     <system.webServer>
       <handlers>
         <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
       </handlers>
       <httpPlatform processPath="%JAVA_HOME%\bin\java.exe"
           arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\gs-spring-boot-0.1.0.jar&quot;">
       </httpPlatform>
     </system.webServer>
   </configuration>
   ```

1. Mentés után a *web.config* fájlt a rendszer, a webes alkalmazás URL-címet, felhasználónevet és jelszót használó Ez az oktatóanyag előző szakaszából FTP-n keresztül csatlakozni. Példa:
   ```
   ftp
   open waws-prod-sn0-000.ftp.azurewebsites.windows.net
   user wingtiptoys-springboot\wingtiptoysuser
   pass ********
   ```

1. A távoli könyvtár módosítsa a webalkalmazás gyökérmappájában (amely */hely/wwwroot*), majd másolja a JAR-fájlra a rugó rendszerindító alkalmazás és a *web.config* a korábbi. Példa:
   ```
   cd site/wwwroot
   put gs-spring-boot-0.1.0.jar
   put web.config
   ```

1. Miután telepítette a JAR és *web.config* a webalkalmazás fájlokat, újra kell indítania a webalkalmazás az Azure portál használatával:

   ![][AZ10]

1. A webes alkalmazás tesztelése a keresse meg a webalkalmazás URL-címre, egy webböngésző segítségével, vagy használja a szintaxist, az alábbi példához hasonló, ha a curl érhető el:
   ```
   curl http://wingtiptoys-springboot.azurewebsites.net/
   ```

1. A következő üzenet jelenik meg: **hónap rugó rendszerindításból!**

   ![Keresse meg a mintaalkalmazás][SB02]

## <a name="next-steps"></a>Következő lépések

Azure rugó rendszerindító alkalmazások használatával kapcsolatos további információkért tekintse meg a következő cikkeket:

* [A rugó rendszerindító alkalmazás Linux az az Azure Tárolószolgáltatásban központi telepítése](../container-service/kubernetes/container-service-deploy-spring-boot-app-on-linux.md)

* [A rugó rendszerindító alkalmazás az az Azure Tárolószolgáltatásban Kubernetes fürt központi telepítése](../container-service/kubernetes/container-service-deploy-spring-boot-app-on-kubernetes.md)

Az Azure Javával való használatáról további információ: [Azure Java fejlesztői központ] és [Java-eszközök a Visual Studio Team Serviceshez].

További információt az Azure-bA depoying webalkalmazások használata FTP, lásd: [telepítse az alkalmazást az Azure App Service segítségével FTP/S].

A rugó rendszerindító mintaprojektet további részleteiért lásd: [rugó rendszerindító bevezetés].

Első lépések a saját rugó rendszerindító alkalmazásokkal kapcsolatban lásd: a **rugó Initializr** https://start.spring.io/ címen.

A webalkalmazás további beállítások konfigurálásával kapcsolatos további információkért lásd: [webes alkalmazások konfigurálása az Azure App Service].

<!-- URL List -->

[Azure App Service]: https://azure.microsoft.com/services/app-service/
[Azure Container Service]: https://azure.microsoft.com/services/container-service/
[Azure Java fejlesztői központ]: https://azure.microsoft.com/develop/java/
[Azure-portálon]: https://portal.azure.com/
[webes alkalmazások konfigurálása az Azure App Service]: /azure/app-service/web-sites-configure
[telepítse az alkalmazást az Azure App Service segítségével FTP/S]: https://docs.microsoft.com/azure/app-service/app-service-deploy-ftp
[ingyenes Azure-fiókot]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java fejlesztői készlet (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[Java-eszközök a Visual Studio Team Serviceshez]: https://java.visualstudio.com/
[Maven]: http://maven.apache.org/
[MSDN-előfizetői előnyeit]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[ Rendszerindító érintkező]: http://projects.spring.io/spring-boot/
[rugó rendszerindító bevezetés]: https://github.com/spring-guides/gs-spring-boot
[rugó keretrendszer]: https://spring.io/

<!-- IMG List -->

[SB01]: ./media/app-service-deploy-spring-boot-web-app-on-azure/SB01.png
[SB02]: ./media/app-service-deploy-spring-boot-web-app-on-azure/SB02.png

[AZ01]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ01.png
[AZ02]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ02.png
[AZ03]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ03.png
[AZ04]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ04.png
[AZ05]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ05.png
[AZ06]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ06.png
[AZ07]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ07.png
[AZ08]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ08.png
[AZ09]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ09.png
[AZ10]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ10.png
