---
title: Azure-gyorsítótár kötése az Azure Spring Cloud-alkalmazás Redis
description: Ismerje meg, hogyan köthető az Azure cache a Redis Azure Spring Cloud-alkalmazásához
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/31/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 6bbd54be46effe324199639477f9ca4ab31bea98
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091402"
---
# <a name="bind-azure-cache-for-redis-to-your-azure-spring-cloud-application"></a>Azure-gyorsítótár kötése az Azure Spring Cloud-alkalmazás Redis 

A Spring boot-alkalmazások manuális konfigurálása helyett az Azure Spring Cloud használatával automatikusan köthető az Azure-szolgáltatások kiválasztásához az alkalmazásaihoz. Ez a cikk bemutatja, hogyan köthető az alkalmazás az Azure cache-hez a Redis.

## <a name="prerequisites"></a>Előfeltételek

* Üzembe helyezett Azure Spring Cloud-példány
* Azure cache a Redis Service-példányhoz
* Azure Spring Cloud-bővítmény az Azure CLI-hez

Ha nem rendelkezik telepített Azure Spring Cloud-példánnyal, kövesse az [Azure Spring Cloud-alkalmazás üzembe helyezésének](spring-cloud-quickstart-launch-app-portal.md)rövid útmutatójában ismertetett lépéseket.

## <a name="bind-azure-cache-for-redis"></a>Az Azure cache kötése a Redis-hez

1. Adja hozzá a következő függőséget a projekt pom.xml fájljához:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. `spring.redis.*`A fájl összes tulajdonságának `application.properties` eltávolítása

1. Frissítse az aktuális telepítést a használatával `az spring-cloud app update` , vagy hozzon létre egy új központi telepítést a használatával `az spring-cloud app deployment create` .

1. Lépjen a Azure Portal Azure Spring Cloud Service oldalára. Nyissa meg az **alkalmazás irányítópultját** , és válassza ki azt az alkalmazást, amelyet az Azure cache-hez szeretne kötni a Redis. Ez az alkalmazás ugyanaz, mint amelyet az előző lépésben frissített vagy telepített.

1. Válassza ki a **szolgáltatási kötés** elemet, majd válassza a **szolgáltatás kötésének létrehozása**lehetőséget. Töltse **ki az űrlapot** , és ügyeljen arra, hogy válassza ki az **Azure cache Redis**, a Redis-kiszolgáló Azure-gyorsítótárát és az **elsődleges** kulcs beállítást.

1. Indítsa újra az alkalmazást. A kötésnek most már működnie kell.

1. A szolgáltatási kötés helyességének biztosításához válassza ki a kötés nevét, és ellenőrizze annak részleteit. A `property` mezőnek így kell kinéznie:
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>További lépések

Ebből a cikkből megtudhatta, hogyan köthető az Azure Spring Cloud-alkalmazás az Azure cache Redis-hez. Ha többet szeretne megtudni az alkalmazáshoz tartozó kötési szolgáltatásokról, tekintse meg a [kötés egy Azure Database for MySQL-példányhoz](spring-cloud-tutorial-bind-mysql.md)című témakört.
