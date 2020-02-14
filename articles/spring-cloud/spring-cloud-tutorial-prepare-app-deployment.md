---
title: Oktatóanyag – Java Spring-alkalmazás előkészítése az Azure Spring Cloud üzembe helyezéséhez
description: Ebben az oktatóanyagban egy Java Spring-alkalmazást készít elő az Azure Spring Cloud üzembe helyezéséhez.
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 02/03/2020
ms.author: brendm
ms.openlocfilehash: af3611e4c4d1f5d8ca52b3ceb80d79dcfd7d2061
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190738"
---
# <a name="prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Java Spring-alkalmazás előkészítése az Azure Spring Cloud üzembe helyezéséhez

Ez a rövid útmutató bemutatja, hogyan készíthet meglévő Java Spring-alkalmazást az Azure Spring Cloud üzembe helyezéséhez. Ha megfelelően van konfigurálva, az Azure Spring Cloud robusztus szolgáltatásokat biztosít a Java Spring Cloud-alkalmazás monitorozásához, méretezéséhez és frissítéséhez.

## <a name="java-runtime-version"></a>Java futtatókörnyezet verziója

Csak a Spring/Java-alkalmazások futhatnak az Azure Spring Cloud-ban.

Az Azure Spring Cloud a Java 8 és a Java 11 használatát is támogatja. Az üzemeltetési környezet tartalmazza az Azure-hoz készült Azul Zulu OpenJDK legújabb verzióját. További információ az Azure-hoz készült Azul Zulu OpenJDK: [a JDK telepítése](https://docs.microsoft.com/azure/java/jdk/java-jdk-install).

## <a name="spring-boot-and-spring-cloud-versions"></a>A Spring boot és a Spring Cloud verziói

Az Azure Spring Cloud csak a Spring boot-alkalmazásokat támogatja. Támogatja a Spring boot 2,1-es és 2,2-es verzióját is. A következő táblázat a támogatott Spring boot és Spring felhő kombinációkat sorolja fel:

Spring boot-verzió | Tavaszi felhő verziója
---|---
2.1 | Greenwich. RELEASE
2.2 | Hoxton. RELEASE

Győződjön meg arról, hogy a Pom. xml fájl megfelelő Spring boot és Spring Cloud függőségekkel rendelkezik a Spring boot-verzió alapján.

### <a name="dependencies-for-spring-boot-version-21"></a>A Spring boot 2,1-es verziójának függőségei

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.12.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Greenwich.SR4</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="dependencies-for-spring-boot-version-22"></a>A Spring boot 2,2-es verziójának függőségei

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.4.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR1</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

## <a name="azure-spring-cloud-client-dependency"></a>Azure Spring Cloud ügyfél-függőség

Az Azure Spring Cloud üzemelteti és kezeli a Spring Cloud-összetevőket. Ilyen összetevők például a Spring Cloud Service Registry és a Spring Cloud config Server. Vegye fel az Azure Spring Cloud ügyféloldali kódtárat a függőségeibe, hogy lehetővé váljon a kommunikáció az Azure Spring Cloud Service-példánnyal.

A következő táblázat felsorolja a Spring boot és Spring Cloud-t használó alkalmazás megfelelő Azure Spring Cloud-verzióit.

Spring boot-verzió | Tavaszi felhő verziója | Azure Spring Cloud-verzió
---|---|---
2.1 | Greenwich. RELEASE | 2.1
2.2 | Hoxton. RELEASE | 2.2

Adja meg a következő függőségek egyikét a Pom. xml fájlban. Válassza ki azt a függőséget, amelynek az Azure Spring Cloud-verziója megfelel a saját igényeinek.

### <a name="dependency-for-azure-spring-cloud-version-21"></a>Az Azure Spring Cloud 2,1-es verziójának függősége

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.1</version>
</dependency>
```

### <a name="dependency-for-azure-spring-cloud-version-22"></a>Az Azure Spring Cloud 2,2-es verziójának függősége

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.2.0</version>
</dependency>
```

## <a name="other-required-dependencies"></a>Egyéb szükséges függőségek

Az Azure Spring Cloud beépített funkcióinak engedélyezéséhez az alkalmazásnak a következő függőségeket kell tartalmaznia. Ez az integráció biztosítja, hogy az alkalmazás helyesen konfigurálja az egyes összetevőket.  

### <a name="service-registry-dependency"></a>Szolgáltatás beállításjegyzékbeli függősége

A felügyelt Azure szolgáltatás beállításjegyzék-szolgáltatásának használatához adja meg a `spring-cloud-starter-netflix-eureka-client` függőséget a Pom. xml fájlban az itt látható módon:

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

A szolgáltatás beállításjegyzék-kiszolgálójának végpontját a rendszer automatikusan környezeti változóként adja meg az alkalmazásban. Az alkalmazások regisztrálhatják magukat a szolgáltatás beállításjegyzék-kiszolgálójával, és felfedezhetik a többi függő szolgáltatást is.

### <a name="distributed-configuration-dependency"></a>Elosztott konfiguráció függősége

Az elosztott konfiguráció engedélyezéséhez vegye fel a következő `spring-cloud-config-client` függőséget a Pom. xml fájl függőségek szakaszába:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Ne adja meg `spring.cloud.config.enabled=false` a bootstrap konfigurációjában. Ellenkező esetben az alkalmazás nem működik a konfigurációs kiszolgálóval.

### <a name="metrics-dependency"></a>Metrikák függősége

Foglalja bele a `spring-boot-starter-actuator` függőséget a Pom. xml fájl függőségek szakaszába, ahogy az itt látható:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 A metrikák rendszeres időközönként a JMX-végpontokról vannak leképezve. A metrikákat a Azure Portal használatával jelenítheti meg.

### <a name="distributed-tracing-dependency"></a>Elosztott nyomkövetési függőség

Adja meg a következő `spring-cloud-starter-sleuth` és `spring-cloud-starter-zipkin` függőségeket a Pom. xml fájl függőségek szakaszában:

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

 Emellett engedélyeznie kell egy Azure Application Insights-példány működését az Azure Spring Cloud Service-példányával. Olvassa el az [elosztott nyomkövetéssel foglalkozó oktatóanyagot](spring-cloud-tutorial-distributed-tracing.md) , amelyből megtudhatja, hogyan használhatja a Application Insightst az Azure Spring Cloud használatával.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan konfigurálhatja a Java Spring-alkalmazást az Azure Spring Cloud üzembe helyezéséhez. A konfigurációs kiszolgálópéldány beállításának megismeréséhez folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan állíthat be egy konfigurációs kiszolgálópéldány](spring-cloud-tutorial-config-server.md)

További minták érhetők el a GitHubon: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).
