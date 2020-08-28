---
title: Java Spring-alkalmazás előkészítése az Azure Spring Cloud üzembe helyezéséhez
description: Ismerje meg, hogyan készíthet egy Java Spring-alkalmazást az Azure Spring Cloud üzembe helyezéséhez.
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/03/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 1261e36c782068844712908e62a72b77d34ba5af
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89046731"
---
# <a name="prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Java Spring-alkalmazás előkészítése az Azure Spring Cloud üzembe helyezéséhez

Ez a témakör bemutatja, hogyan készíthet meglévő Java Spring-alkalmazást az Azure Spring Cloud üzembe helyezéséhez. Ha megfelelően van konfigurálva, az Azure Spring Cloud robusztus szolgáltatásokat biztosít a Java Spring Cloud-alkalmazás monitorozásához, méretezéséhez és frissítéséhez.

A példa futtatása előtt próbálja ki az [alapszintű](spring-cloud-quickstart.md)rövid útmutatót.

Más példák azt is ismertetik, hogyan helyezhet üzembe egy alkalmazást az Azure Spring-felhőben, ha a POM-fájl konfigurálva van. 
* [Alkalmazás elindítása a Azure Portal használatával](spring-cloud-quickstart-launch-app-portal.md)
* [Alkalmazás elindítása az Azure CLI használatával](spring-cloud-quickstart-launch-app-cli.md)

Ez a cikk a szükséges függőségeket és azok hozzáadását ismerteti a POM-fájlhoz.

## <a name="java-runtime-version"></a>Java futtatókörnyezet verziója

Csak a Spring/Java-alkalmazások futhatnak az Azure Spring Cloud-ban.

Az Azure Spring Cloud a Java 8 és a Java 11 használatát is támogatja. Az üzemeltetési környezet tartalmazza az Azure-hoz készült Azul Zulu OpenJDK legújabb verzióját. További információ az Azure-hoz készült Azul Zulu OpenJDK: [a JDK telepítése](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install).

## <a name="spring-boot-and-spring-cloud-versions"></a>A Spring boot és a Spring Cloud verziói

Egy meglévő Spring boot-alkalmazás előkészítéséhez az Azure Spring Cloud-ba történő üzembe helyezéshez az alkalmazás POM-fájljának Spring boot és Spring Cloud függőségei szerepelnek, ahogy az a következő részben látható.

Az Azure Spring Cloud csak Spring boot-alkalmazásokat támogat, vagy a Spring boot 2,1-es vagy 2,2-os verzióját. A következő táblázat a támogatott Spring boot és Spring felhő kombinációkat sorolja fel:

Spring boot-verzió | Tavaszi felhő verziója
---|---
2.1 | Greenwich. RELEASE
2,2 | Hoxton. RELEASE
2.3 | Hoxton. SR5

### <a name="dependencies-for-spring-boot-version-21"></a>A Spring boot 2,1-es verziójának függőségei

A Spring boot 2,1-es verziójában adja hozzá a következő függőségeket az Application POM-fájlhoz.

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

A Spring boot 2,2-es verziójában adja hozzá a következő függőségeket az Application POM-fájlhoz.

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
### <a name="dependencies-for-spring-boot-version-23"></a>A Spring boot 2,3-es verziójának függőségei

A Spring boot 2,3-es verziójában adja hozzá a következő függőségeket az Application POM-fájlhoz.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.0.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR5</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```
## <a name="azure-spring-cloud-client-dependency"></a>Azure Spring Cloud ügyfél-függőség

Az Azure Spring Cloud üzemelteti és kezeli a Spring Cloud-összetevőket. Az összetevők közé tartoznak a Spring Cloud Service Registry és a Spring Cloud config Server. Vegye fel az Azure Spring Cloud ügyféloldali kódtárat a függőségeibe, hogy lehetővé váljon a kommunikáció az Azure Spring Cloud Service-példánnyal.

A következő táblázat felsorolja a Spring boot és Spring Cloud-t használó alkalmazás megfelelő Azure Spring Cloud-verzióit.

Spring boot-verzió | Tavaszi felhő verziója | Azure Spring Cloud-verzió
---|---|---
2.1 | Greenwich. RELEASE | 2.1
2,2 | Hoxton. RELEASE | 2,2
2.3 | Hoxton. SR5 | 2.3

Adja meg a következő függőségek egyikét a pom.xml fájlban. Válassza ki azt a függőséget, amelynek az Azure Spring Cloud-verziója megfelel a saját igényeinek.

### <a name="dependency-for-azure-spring-cloud-version-21"></a>Az Azure Spring Cloud 2,1-es verziójának függősége

A Spring boot 2,1-es verziójában adja hozzá a következő függőséget az alkalmazás POM-fájljához.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.2</version>
</dependency>
```

### <a name="dependency-for-azure-spring-cloud-version-22"></a>Az Azure Spring Cloud 2,2-es verziójának függősége

A Spring boot 2,2-es verziójában adja hozzá a következő függőséget az alkalmazás POM-fájljához.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.2.1</version>
</dependency>
```

A Spring boot 2,3-es verziójában adja hozzá a következő függőséget az alkalmazás POM-fájljához.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.3.0</version>
</dependency>
```

## <a name="other-recommended-dependencies-to-enable-azure-spring-cloud-features"></a>Egyéb ajánlott függőségek az Azure Spring Cloud funkcióinak engedélyezéséhez

Annak érdekében, hogy az Azure Spring Cloud beépített funkciói a szolgáltatás-beállításjegyzékből az elosztott nyomkövetésre legyenek kialakítva, a következő függőségeket is meg kell adnia az alkalmazásban. Ezen függőségek némelyikét elhúzhatja, ha nincs szüksége a megfelelő szolgáltatásokra az adott alkalmazásokhoz.

### <a name="service-registry"></a>Szolgáltatás beállításjegyzéke

A felügyelt Azure szolgáltatás beállításjegyzék-szolgáltatásának használatához adja `spring-cloud-starter-netflix-eureka-client` meg a függőséget a pom.xml fájlban az itt látható módon:

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

A szolgáltatás beállításjegyzék-kiszolgálójának végpontját a rendszer automatikusan környezeti változóként adja meg az alkalmazásban. Az alkalmazások regisztrálhatják magukat a szolgáltatás beállításjegyzék-kiszolgálójával, és felfedezhetik a többi függő szolgáltatást is.

#### <a name="enablediscoveryclient-annotation"></a>EnableDiscoveryClient jegyzet

Adja hozzá a következő megjegyzést az alkalmazás forráskódja számára.
```java
@EnableDiscoveryClient
```
Például tekintse meg a korábbi példákból származó piggymetrics alkalmazást:
```java
package com.piggymetrics.gateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;

@SpringBootApplication
@EnableDiscoveryClient
@EnableZuulProxy

public class GatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(GatewayApplication.class, args);
    }
}
```

### <a name="distributed-configuration"></a>Elosztott konfiguráció

Az elosztott konfiguráció engedélyezéséhez vegye fel a következő `spring-cloud-config-client` függőséget a pom.xml fájl függőségek szakaszába:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Ne adja meg a `spring.cloud.config.enabled=false` bootstrap konfigurációját. Ellenkező esetben az alkalmazás nem működik a konfigurációs kiszolgálóval.

### <a name="metrics"></a>Mérőszámok

Vegye fel a függőséget a `spring-boot-starter-actuator` pom.xml fájl függőségek szakaszába, ahogy az itt látható:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 A metrikák rendszeres időközönként a JMX-végpontokról vannak leképezve. A metrikákat a Azure Portal használatával jelenítheti meg.

 > [!WARNING]
 > Adja meg a `spring.jmx.enabled=true` konfigurációs tulajdonságot. Ellenkező esetben a metrikák nem megjeleníthetők Azure Portalban.

### <a name="distributed-tracing"></a>Elosztott nyomkövetés

Adja meg a következő `spring-cloud-starter-sleuth` és `spring-cloud-starter-zipkin` függőségeket a pom.xml fájl függőségek szakaszában:

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

 Emellett engedélyeznie kell egy Azure Application Insights-példány működését az Azure Spring Cloud Service-példányával. Az Azure Spring Cloudtel való Application Insights használatáról az [elosztott nyomkövetés dokumentációjában](spring-cloud-tutorial-distributed-tracing.md)talál további információt.

## <a name="see-also"></a>Lásd még
* [Alkalmazások naplófájljainak és metrikáinak elemzése](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)
* [A Config Server beállítása](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-config-server)
* [Elosztott nyomkövetés használata az Azure Spring Cloud használatával](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
* [Spring rövid útmutató](https://spring.io/quickstart)
* [Spring boot – dokumentáció](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>Következő lépések

Ebben a témakörben megtanulta, hogyan konfigurálhatja a Java Spring-alkalmazást az Azure Spring Cloud üzembe helyezéséhez. A konfigurációs kiszolgálópéldány beállításával kapcsolatos további információkért lásd: [konfigurációs kiszolgálói példány beállítása](spring-cloud-tutorial-config-server.md).

További minták érhetők el a GitHubon: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).
