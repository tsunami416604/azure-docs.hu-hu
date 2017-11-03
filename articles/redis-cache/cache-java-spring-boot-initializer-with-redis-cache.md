---
title: "A rugó rendszerindítás-inicializáló alkalmazások Redis gyorsítótár konfigurálása"
description: "Megtudhatja, hogyan konfigurálhatja egy rugó rendszerindító alkalmazást hoz létre a rugó Initializr az Azure Redis Cache használatához."
services: redis-cache
documentationcenter: java
author: rmcmurray
manager: cfowler
editor: 
keywords: "Redis gyorsítótár rugó, rugó rendszerindító Starter,"
ms.assetid: 
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: java
ms.topic: article
ms.date: 08/31/2017
ms.author: robmcm;zhijzhao;yidon
ms.openlocfilehash: 7a6ec549654d00975494bac8594a6777af5ec415
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-a-spring-boot-initializer-app-to-use-redis-cache"></a>A rugó rendszerindítás-inicializáló alkalmazások Redis gyorsítótár konfigurálása

## <a name="overview"></a>Áttekintés

A  **[rugó keretrendszer]**  egy nyílt forráskódú megoldás, amely a fejlesztőket Java vállalati szintű alkalmazásokat. A beépített több népszerű projektek a platform egyik [rugó rendszerindító], amely lehetővé teszi egy egyszerűsített megközelítés önálló Java-alkalmazások létrehozása. Ismerkedés a rugó rendszerindító fejlesztők érdekében több minta rugó rendszerindító csomagok elérhetők, <https://github.com/spring-guides/>. Alapszintű rugó rendszerindító projektek listája mellett a  **[rugó Initializr]**  a fejlesztőket az egyéni rendszerindító rugó-alkalmazások létrehozásának első lépései.

Ez a cikk végigvezeti az Azure portálra, majd a Redis gyorsítótár létrehozása a **rugó Initializr** egyéni alkalmazást, és majd hozza létre, amely tárolja, és lekéri az adatokat, és a Redis Java-webalkalmazás létrehozása gyorsítótár.

## <a name="prerequisites"></a>Előfeltételek

A következő szoftverek szükségesek ahhoz, hogy kövesse a cikkben található lépéseket:

* Azure-előfizetés; Ha még nem rendelkezik Azure-előfizetéssel, aktiválhatja a [MSDN-előfizetői előnyeit] vagy regisztráljon egy [ingyenes Azure-fiókot].

* A [Java fejlesztői készlet (JDK)](http://www.oracle.com/technetwork/java/javase/downloads/), 1.7 vagy újabb verziója.

* [Apache Maven](http://maven.apache.org/), 3.0-s vagy újabb verziója.

## <a name="create-a-redis-cache-on-azure"></a>Redis Cache gyorsítótár létrehozása az Azure-ban

1. Keresse meg az Azure portálon, a <https://portal.azure.com/> , és kattintson az elemre vonatkozó **+ új**.

   ![Azure Portal][AZ01]

1. Kattintson a **adatbázis**, és kattintson a **Redis Cache**.

   ![Azure Portal][AZ02]

1. Az a **új Redis Cache** lapján adja meg a következőket:

   * Adja meg a **DNS-név** a gyorsítótárhoz.
   * Adja meg a **előfizetés**, **erőforráscsoport**, **hely**, és **tarifacsomag**.
   * A jelen oktatóanyag esetében válassza ki a **port 6379 feloldása**.

   > [!NOTE]
   >
   > Redis gyorsítótár SSL használatával, de a különböző Redis-ügyfelek például Jedis használni kell. További információkért lásd: [Azure Redis Cache használata Java][Redis Cache with Java].
   >

   Ezek a beállítások megadását, kattintson a **létrehozása** a gyorsítótár létrehozásához.

   ![Azure Portal][AZ03]

1. Miután befejeződött a gyorsítótárhoz, akkor jelenik meg az Azure megjelenik **irányítópult**, valamint a a **összes erőforrás**, és **Redis Cache-gyorsítótárak** lapokat. Kattintson a gyorsítótár bármely azokon a helyeken, a gyorsítótár Tulajdonságok lapjának megnyitásához.

   ![Azure Portal][AZ04]

1. Amikor megjelenik a gyorsítótár, a tulajdonságok listájának tartalmazó lapon kattintson a **hívóbetűk** , és másolja a tárelérési kulcsok a gyorsítótárhoz.

   ![Azure Portal][AZ05]

## <a name="create-a-custom-application-using-the-spring-initializr"></a>A rugó Initializr használó egyéni alkalmazás létrehozása

1. Keresse meg a <https://start.spring.io/>.

1. Adja meg, hogy szeretné-e létrehozni egy **Maven** a projekt **Java**, adja meg a **csoport** és **Aritifact** az alkalmazás nevét, és kattintson a hivatkozásra kattintva **kapcsoló megadásával teljes verziójúra** a rugó Initializr a.

   ![Basic rugó Initializr beállítások][SI01]

   > [!NOTE]
   >
   > A rugó Initializr fogja használni a **csoport** és **Aritifact** nevek létrehozása a csomag nevét; például: *com.contoso.myazuredemo*.
   >

1. Görgessen le a **webes** szakaszt, és jelölje be a **webes**, majd görgessen le a a **NoSQL** szakaszt, és jelölje be a **Redis**, majd görgessen a lap alján, majd kattintson a gombra **készítése a projekt**.

   ![Teljes rugó Initializr beállítások][SI02]

1. Amikor a rendszer kéri, töltse le a projekt egy elérési utat a helyi számítógépen.

   ![Egyéni rugó rendszerindító-projekt letöltése][SI03]

1. A helyi számítógépen a fájlok kibontását követően az egyéni rugó rendszerindító alkalmazás készen áll a szerkesztésre lesz.

   ![Egyéni rugó rendszerindító project fájlok][SI04]

## <a name="configure-your-custom-spring-boot-to-use-your-redis-cache"></a>Az egyéni rugó rendszerindító a Redis gyorsítótár konfigurálása

1. Keresse meg a *application.properties* fájlt a *erőforrások* könyvtárhoz, az alkalmazás vagy a fájl létrehozására, ha még nem létezik.

   ![Keresse meg a application.properties fájlt][RE01]

1. Nyissa meg a *application.properties* fájlt egy szövegszerkesztőben, és adja hozzá a következő sorokat a fájlhoz, és cserélje le a minta értékek a megfelelő tulajdonságokat a gyorsítótárból:

   ```yaml
   # Specify the DNS URI of your Redis cache.
   spring.redis.host=myspringbootcache.redis.cache.windows.net

   # Specify the port for your Redis cache.
   spring.redis.port=6379

   # Specify the access key for your Redis cache.
   spring.redis.password=57686f6120447564652c2049495320526f636b73=
   ```

   ![A application.properties fájl szerkesztése][RE02]

   > [!NOTE]
   >
   > Ha a különböző Redis-ügyfelek például, amely engedélyezi az SSL Jedis használta, port 6380 megadni a *application.properties* fájlt. További információkért lásd: [Azure Redis Cache használata Java][Redis Cache with Java].
   >

1. Mentse és zárja be a *application.properties* fájlt.

1. Hozza létre a *vezérlő* alatt a forrásmappát a csomag; például:

   `C:\SpringBoot\myazuredemo\src\main\java\com\contoso\myazuredemo\controller`

   – vagy –

   `/users/example/home/myazuredemo/src/main/java/com/contoso/myazuredemo/controller`

1. Hozzon létre egy új fájlt *HelloController.java* a a *vezérlő* mappát. Nyissa meg a fájlt egy szövegszerkesztőben, és az alábbi kód hozzáadása:

   ```java
   package com.contoso.myazuredemo;

   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.RestController;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.data.redis.core.StringRedisTemplate;
   import org.springframework.data.redis.core.ValueOperations;

   @RestController
   public class HelloController {
   
      @Autowired
      private StringRedisTemplate template;

      @RequestMapping("/")
      // Define the Hello World controller.
      public String hello() {
      
         ValueOperations<String, String> ops = this.template.opsForValue();

         // Add a Hello World string to your cache.
         String key = "greeting";
         if (!this.template.hasKey(key)) {
             ops.set(key, "Hello World!");
         }

         // Return the string from your cache.
         return ops.get(key);
      }
   }
   ```
   
   Hol kell lecserélni `com.contoso.myazuredemo` a projekt nevét.

1. Mentse és zárja be a *HelloController.java* fájlt.

1. A rugó rendszerindító alkalmazás Maven létrehozása és futtatása. Példa:

   ```shell
   mvn clean package
   mvn spring-boot:run
   ```

1. A webes alkalmazás tesztelése a webböngésző segítségével 8080 tallózással, vagy használja a szintaxist, az alábbi példához hasonló, ha a curl érhető el:

   ```shell
   curl http://localhost:8080
   ```

   Láthatja, hogy a "Hello World!" üzenet tartományvezérlőről a minta jelenik meg, amely dinamikusan lekérésére a Redis-gyorsítótárból.

## <a name="next-steps"></a>Következő lépések

Azure rugó rendszerindító alkalmazások használatával kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Az Azure App Service egy rugó rendszerindító alkalmazás központi telepítése](../app-service/app-service-deploy-spring-boot-web-app-on-azure.md)

* [Az az Azure Tárolószolgáltatásban Kubernetes fürt rugó rendszerindító alkalmazást futtat](../container-service/container-service-deploy-spring-boot-app-on-kubernetes.md)

Az Azure Javával való használatáról további információ: [Azure Java fejlesztői központ] és [Java-eszközök a Visual Studio Team Serviceshez].

További információ a kezdeti Redis Cache használatának az Azure, a Java című [Azure Redis Cache használata Java][Redis Cache with Java].

<!-- URL List -->

[Azure Java fejlesztői központ]: https://azure.microsoft.com/develop/java/
[ingyenes Azure-fiókot]: https://azure.microsoft.com/pricing/free-trial/
[Java-eszközök a Visual Studio Team Serviceshez]: https://java.visualstudio.com/
[MSDN-előfizetői előnyeit]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[rugó rendszerindító]: http://projects.spring.io/spring-boot/
[rugó Initializr]: https://start.spring.io/
[rugó keretrendszer]: https://spring.io/
[Redis Cache with Java]: cache-java-get-started.md

<!-- IMG List -->

[AZ01]: ./media/cache-java-spring-boot-initializer-with-redis-cache/AZ01.png
[AZ02]: ./media/cache-java-spring-boot-initializer-with-redis-cache/AZ02.png
[AZ03]: ./media/cache-java-spring-boot-initializer-with-redis-cache/AZ03.png
[AZ04]: ./media/cache-java-spring-boot-initializer-with-redis-cache/AZ04.png
[AZ05]: ./media/cache-java-spring-boot-initializer-with-redis-cache/AZ05.png

[SI01]: ./media/cache-java-spring-boot-initializer-with-redis-cache/SI01.png
[SI02]: ./media/cache-java-spring-boot-initializer-with-redis-cache/SI02.png
[SI03]: ./media/cache-java-spring-boot-initializer-with-redis-cache/SI03.png
[SI04]: ./media/cache-java-spring-boot-initializer-with-redis-cache/SI04.png

[RE01]: ./media/cache-java-spring-boot-initializer-with-redis-cache/RE01.png
[RE02]: ./media/cache-java-spring-boot-initializer-with-redis-cache/RE02.png
