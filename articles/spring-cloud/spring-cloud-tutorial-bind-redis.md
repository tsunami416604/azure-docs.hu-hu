---
title: Oktatóanyag – Az Azure-gyorsítótár kötése a Redis-hez az Azure Spring Cloud-alkalmazáshoz
description: Ez az oktatóanyag bemutatja, hogyan kötheti össze az Azure Cache for Redis-t az Azure Spring Cloud-alkalmazáshoz
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: brendm
ms.openlocfilehash: 94f7b5a2363b7c53e0f70500e5a0a8cb6f64e611
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76277506"
---
# <a name="bind-azure-cache-for-redis-to-your-azure-spring-cloud-application"></a>Az Azure Cache for Redis kötése az Azure Spring Cloud-alkalmazáshoz 

Ahelyett, hogy manuálisan konfigurálna a tavaszi rendszerindítási alkalmazásokat, automatikusan összekötheti a kiválasztott Azure-szolgáltatásokat az alkalmazásokhoz az Azure Spring Cloud használatával. Ez a cikk bemutatja, hogyan kötheti az alkalmazást az Azure Cache for Redis.

## <a name="prerequisites"></a>Előfeltételek

* Üzembe helyezett Azure Spring Cloud-példány
* Azure-gyorsítótár a Redis szolgáltatáspéldányhoz
* Az Azure Spring Cloud bővítmény az Azure CLI-hez

Ha nem rendelkezik üzembe helyezett Azure Spring Cloud-példánysal, kövesse az [Azure Spring Cloud-alkalmazások üzembe helyezésének rövid útmutatójában](spring-cloud-quickstart-launch-app-portal.md)leírt lépéseket.

## <a name="bind-azure-cache-for-redis"></a>Azure-gyorsítótár kötése a Redis-hez

1. Adja hozzá a következő függőséget a projekt pom.xml fájljához:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. Tulajdonságok `spring.redis.*` eltávolítása a `application.properties` fájlból

1. Frissítse az aktuális `az spring-cloud app update` központi telepítést `az spring-cloud app deployment create`a használatával, vagy hozzon létre egy új központi telepítést a használatával.

1. Nyissa meg az Azure Spring Cloud szolgáltatáslapját az Azure Portalon. Nyissa meg **az alkalmazásirányítópultot,** és válassza ki az Azure-gyorsítótárhoz a Redis-hez kötéshez szükséges alkalmazást. Ez az alkalmazás megegyezik az előző lépésben frissített vagy telepített alkalmazással.

1. Válassza a **Szolgáltatáskötés** lehetőséget, majd a **Szolgáltatáskötés létrehozása**lehetőséget. Töltse ki az űrlapot, ügyelve arra, hogy válassza ki a **kötési típusú** érték **Azure Cache for Redis,** az Azure Cache for Redis server, és az **elsődleges** kulcs opciót.

1. Indítsa újra az alkalmazást. A kötésnek működnie kell.

1. A szolgáltatáskötés helyességének biztosításához jelölje ki a kötés nevét, és ellenőrizze annak részleteit. A `property` mező nek így kell kinéznie:
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan kötheti össze az Azure Spring Cloud-alkalmazást az Azure Cache for Redis-hez. Ha többet szeretne megtudni az alkalmazáshoz való kötési szolgáltatásokról, folytassa az alkalmazás mySQL-példányhoz való kötéséről szóló oktatóanyaggal.

> [!div class="nextstepaction"]
> [Megtudhatja, hogyan köthető kontorékegy Azure-adatbázishoz a MySQL-példányhoz](spring-cloud-tutorial-bind-mysql.md)
