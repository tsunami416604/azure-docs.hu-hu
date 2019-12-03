---
title: Oktatóanyag – Spring-alkalmazás előkészítése az Azure Spring Cloud üzembe helyezéséhez
description: Ebben az oktatóanyagban egy Java Spring-alkalmazást készít elő az üzembe helyezéshez.
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 3c567c54f887afe9ac112970a682b9466515d949
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708737"
---
# <a name="tutorial-prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Oktatóanyag: Java Spring-alkalmazás előkészítése az Azure Spring Cloud üzembe helyezéséhez

Ez a rövid útmutató bemutatja, hogyan készíthet meglévő Java Spring Cloud-alkalmazást az Azure Spring Cloud-hoz való üzembe helyezéshez.  Megfelelően konfigurálva az Azure Spring Cloud robusztus szolgáltatásokat biztosít a Spring Cloud-alkalmazás monitorozásához, méretezéséhez és frissítéséhez. 

## <a name="java-runtime-version"></a>Java futtatókörnyezet verziója

Csak a Spring/Java-alkalmazások futhatnak az Azure Spring Cloud-ban.

A Java 8 és a Java 11 is támogatott. Az üzemeltetési környezet tartalmazza az Azure-hoz készült legújabb Azul Zulu-OpenJDK. Az Azure-hoz készült Azul Zulu OpenJDK kapcsolatos további információkért tekintse meg [ezt a cikket](https://docs.microsoft.com/azure/java/jdk/java-jdk-install) . 

## <a name="spring-boot-and-spring-cloud-versions"></a>A Spring boot és a Spring Cloud verziói

Az Azure Spring Cloud-ban csak a Spring boot-alkalmazások támogatottak. A Spring boot 2,0 és a 2,1 egyaránt támogatott. A Spring boot és Spring Felhőbeli kombinációk a következő táblázatban láthatók.

Spring boot-verzió | Tavaszi felhő verziója
---|---
2.0. x | Finchley. RELEASE
2.1. x | Greenwich. RELEASE

Ellenőrizze, hogy a `pom.xml`-fájl a verzió alapján a Spring boot és a Spring Cloud függőségeivel rendelkezik-e.

### <a name="version-20"></a>2,0-es verzió:

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.9.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Finchley.SR4</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="version-21"></a>2,1-es verzió:

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.8.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Greenwich.SR3</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

## <a name="azure-spring-cloud-client-dependency"></a>Azure Spring Cloud ügyfél-függőség

Az Azure Spring Cloud üzemelteti és kezeli a tavaszi Felhőbeli összetevőket, például a Spring Cloud Service regisztrációs adatbázisát és a Spring Cloud config-kiszolgálót. A függőségek között az Azure Spring Cloud ügyféloldali könyvtára is elérhető, amely lehetővé teszi a kommunikációt az Azure Spring Cloud Service-példánnyal.

Az alábbi táblázat a Spring boot/Spring Cloud alkalmazás megfelelő verzióit sorolja fel.

Spring boot-verzió | Tavaszi felhő verziója | Azure Spring Cloud-verzió
---|---|---
2.0. x | Finchley. RELEASE | 2.0. x
2.1. x | Greenwich. RELEASE | 2.1. x

Adja meg az alábbi kódrészletek egyikét a `pom.xml`ban.  Válassza ki azt a kódrészletet, amelynek a verziója megegyezik a saját verziójával.

### <a name="version-20x"></a>2\.0. x verzió:
```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.0.0</version>
</dependency>
```

### <a name="version-21x"></a>2\.1. x verzió:
```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
</dependency>
```

## <a name="other-required-dependencies"></a>Egyéb szükséges függőségek

Az Azure Spring Cloud beépített funkcióinak engedélyezéséhez az alkalmazásnak a következő függőségeket kell tartalmaznia. Ez biztosítja, hogy az alkalmazás helyesen konfigurálja az egyes összetevőket.  

### <a name="service-registry"></a>Szolgáltatás beállításjegyzéke

A felügyelt Azure-szolgáltatás beállításjegyzék-szolgáltatásának használatához az alábbi ábrán látható `POM.xml` `spring-cloud-starter-netflix-eureka-client` belefoglalása.

A szolgáltatás beállításjegyzék-kiszolgálójának végpontja automatikusan környezeti változókként lesz befecskendezve az alkalmazással. Az alkalmazások regisztrálhatják magukat a szolgáltatás beállításjegyzék-kiszolgálójával, és felfedezhetik a többi függő szolgáltatást is.

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

### <a name="distributed-configuration"></a>Elosztott konfiguráció

Az elosztott konfiguráció engedélyezéséhez vegyen fel `spring-cloud-config-client`t a `pom.xml`függőségek részében.

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Ne határozza meg `spring.cloud.config.enabled=false` a rendszerindítási konfigurációban, mert az alkalmazás nem fog működni a konfigurációs kiszolgálóval.

### <a name="metrics"></a>Metrikák

`spring-boot-starter-actuator` belefoglalása a Pom. XML függőségek szakaszába. A metrikák rendszeres időközönként le lesznek húzva a JMX-végpontokról, és a Azure Portal használatával megjeleníthetők.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

### <a name="distributed-tracing"></a>Elosztott nyomkövetés

Az alábbi módon vegyen fel `spring-cloud-starter-sleuth` és `spring-cloud-starter-zipkin` a Pom. XML függőségek szakaszába. Emellett engedélyeznie kell egy Azure app bepillantást az Azure Spring Cloud Service-példányával. További információ az App bepillantást az Azure Spring Cloudtel című témakörben [talál](spring-cloud-tutorial-distributed-tracing.md)

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan konfigurálhatja a Java Spring-alkalmazást az Azure Spring Cloud üzembe helyezéséhez.  Ha szeretné megtudni, hogyan engedélyezheti a konfigurációs kiszolgálót, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan állíthatja be a konfigurációs kiszolgálót](spring-cloud-tutorial-config-server.md).
