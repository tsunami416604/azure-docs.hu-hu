---
title: Oktatóanyag – az Azure cache kötése az Azure Spring Cloud-alkalmazás Redis
description: Ebből az oktatóanyagból megtudhatja, hogyan köti össze az Azure cache-t az Azure Spring Cloud-alkalmazás Redis
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: 662d36f8a25f2f0a21d800b7b1a25e94b13908a7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75461498"
---
# <a name="bind-azure-cache-for-redis-to-your-azure-spring-cloud-application"></a>Azure-gyorsítótár kötése az Azure Spring Cloud-alkalmazás Redis 

A Spring boot-alkalmazások manuális konfigurálása helyett az Azure Spring Cloud használatával automatikusan köthető az Azure-szolgáltatások kiválasztásához az alkalmazásaihoz. Ez a cikk bemutatja, hogyan köthető az alkalmazás az Azure cache-hez a Redis.

## <a name="prerequisites"></a>Előfeltételek

* Üzembe helyezett Azure Spring Cloud-példány
* Azure cache a Redis Service-példányhoz
* Azure Spring Cloud-bővítmény az Azure CLI-hez

Ha nem rendelkezik telepített Azure Spring Cloud-példánnyal, kövesse az [Azure Spring Cloud-alkalmazás üzembe helyezésének](spring-cloud-quickstart-launch-app-portal.md)rövid útmutatójában ismertetett lépéseket.

## <a name="bind-azure-cache-for-redis"></a>Az Azure cache kötése a Redis-hez

1. Adja hozzá a következő függőséget a projekt Pom. XML fájljához:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. Távolítsa el a `spring.redis.*` tulajdonságokat a `application.properties` fájlból

1. Frissítse az aktuális telepítést `az spring-cloud app update` használatával, vagy hozzon létre egy új központi telepítést a `az spring-cloud app deployment create`használatával.

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

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan köthető az Azure Spring Cloud-alkalmazás az Azure cache-hez a Redis. Ha többet szeretne megtudni az alkalmazáshoz tartozó kötési szolgáltatásokról, folytassa az alkalmazás kötése Azure Database for MySQL-példányhoz való kötésével foglalkozó oktatóanyagot.

> [!div class="nextstepaction"]
> [Útmutató Azure Database for MySQL-példányhoz való kötéshez](spring-cloud-tutorial-bind-mysql.md)
