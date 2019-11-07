---
title: Azure cache kötése az Azure Spring Cloud-alkalmazáshoz való Redis | Microsoft Docs
description: Ismerje meg, hogyan köthető az Azure cache a Redis Azure Spring Cloud-alkalmazásához
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: a901e4194909df85f53799d5937515e42ea87a69
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607574"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-cache-for-redis"></a>Oktatóanyag: Azure-szolgáltatások kötése Azure Spring Cloud-alkalmazáshoz: Azure cache for Redis

Az Azure Spring Cloud lehetővé teszi, hogy az Azure-szolgáltatásokat automatikusan, a Spring boot-alkalmazás manuális konfigurálása helyett az alkalmazásokhoz kösse. Ez a cikk bemutatja, hogyan köthető az alkalmazás az Azure cache-hez a Redis.

## <a name="prerequisites"></a>Előfeltételek

* Üzembe helyezett Azure Spring Cloud-példány
* Azure cache a Redis Service-példányhoz
* Azure Spring Cloud-bővítmény az Azure CLI-hez

Ha nem rendelkezik telepített Azure Spring Cloud-példánnyal, kövesse az ebben a rövid útmutatóban ismertetett lépéseket az első Spring [Cloud-alkalmazás](spring-cloud-quickstart-launch-app-portal.md) üzembe helyezéséhez.

## <a name="bind-azure-cache-for-redis"></a>Az Azure cache kötése a Redis-hez

1. Adja hozzá a következő függőséget a projekt `pom.xml`

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. Távolítsa el a `spring.redis.*` tulajdonságokat, ha van ilyen, a `application.properties` fájlban

1. Frissítse az aktuális telepítést `az spring-cloud app update` használatával, vagy hozzon létre egy új központi telepítést a `az spring-cloud app deployment create` használatával.

1. Lépjen a Azure Portal Azure Spring Cloud Service oldalára. Keresse meg az **alkalmazás irányítópultját** , és válassza ki azt az alkalmazást, amely a Redis készült Azure cache-hez kötődik.  Ez ugyanaz az alkalmazás, amelyet az előző lépésben frissített vagy telepített. Ezután válassza a `Service binding` elemet, és válassza a `Create service binding` gombot. Töltse ki az űrlapot, és győződjön meg arról, hogy a **kötési típus** `Azure Cache for Redis`, a Redis-kiszolgáló és az elsődleges kulcs lehetőség van kiválasztva. 

1. Indítsa újra az alkalmazást, és ez a kötés most működik.

1. A szolgáltatási kötés helyességének biztosításához válassza ki a kötés nevét, és ellenőrizze annak részleteit. A `property` mezőnek így kell kinéznie:
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>További lépések

Ebből az oktatóanyagból megtudhatta, hogyan köthető az Azure Spring Cloud-alkalmazás egy Azure Redis cache-hez.  Ha többet szeretne megtudni az alkalmazásra vonatkozó kötési szolgáltatásokról, folytassa az alkalmazás egy MySQL-ADATBÁZIShoz való kötésével kapcsolatos oktatóanyagot.

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan köthető egy Azure MySQL-szolgáltatás az Azure Spring Cloud Service-](spring-cloud-tutorial-bind-mysql.md)hez.