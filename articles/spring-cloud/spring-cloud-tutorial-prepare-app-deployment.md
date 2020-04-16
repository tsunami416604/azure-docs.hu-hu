---
title: Útmutató – Java Spring-alkalmazás előkészítése az Azure Spring Cloudban való üzembe helyezéshez
description: Ebben a témakörben előkészíti a Java Spring alkalmazást az Azure Spring Cloud üzembe helyezéséhez.
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/03/2020
ms.author: brendm
ms.openlocfilehash: 025dbc56fb46ef5b6225d35564b8e4ac3c82e6e3
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414459"
---
# <a name="prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Java Spring alkalmazás előkészítése az Azure Spring Cloudban való telepítéshez

Ez a témakör bemutatja, hogyan készíthet elő egy meglévő Java Spring alkalmazást az Azure Spring Cloud üzembe helyezéséhez. Ha megfelelően van konfigurálva, az Azure Spring Cloud robusztus szolgáltatásokat nyújt a Java Spring Cloud-alkalmazás figyeléséhez, méretezéséhez és frissítéséhez.

További példák bemutatják, hogyan telepíthet egy alkalmazást az Azure Spring Cloud szolgáltatásba a POM-fájl konfigurálásakor. 
* [Alkalmazás indítása az Azure Portalon](spring-cloud-quickstart-launch-app-portal.md)
* [Alkalmazás indítása az Azure CLI használatával](spring-cloud-quickstart-launch-app-cli.md)

Ez a cikk ismerteti a szükséges függőségeket, és hogyan kell hozzáadni őket a POM-fájlhoz.

## <a name="java-runtime-version"></a>Java futásidejű verzió

Csak tavaszi/Java alkalmazások futtathatók az Azure Spring Cloud szolgáltatásban.

Az Azure Spring Cloud támogatja a Java 8-at és a Java 11-et is. Az üzemeltetési környezet az Azul Zulu OpenJDK azure-hoz legújabb verzióját tartalmazza. Az Azul Zulu OpenJDK for Azure-ról a [JDK telepítése című](https://docs.microsoft.com/azure/java/jdk/java-jdk-install)témakörben talál további információt.

## <a name="spring-boot-and-spring-cloud-versions"></a>Spring Boot és Spring Cloud verziók

Egy meglévő tavaszi rendszerindítási alkalmazás előkészítése az Azure Spring Cloud üzembe helyezéséhez tartalmazza a spring boot és a spring cloud függőségeket az alkalmazás POM-fájljában, ahogy az a következő szakaszokban látható.

Az Azure Spring Cloud csak a Spring Boot 2.1-es vagy a 2.2-es verziójú tavaszi rendszerindítási alkalmazásokat támogatja. Az alábbi táblázat a támogatott spring boot és spring cloud kombinációkat sorolja fel:

Tavaszi boot verzió | Tavaszi felhő verzió
---|---
2.1 | Greenwich.RELEASE
2,2 | Hoxton.RELEASE

### <a name="dependencies-for-spring-boot-version-21"></a>Függőségek a tavaszi rendszerindítás 2.1-es verziójához

A Spring Boot 2.1-es verziójához adja hozzá a következő függőségeket az alkalmazás POM-fájljába.

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

### <a name="dependencies-for-spring-boot-version-22"></a>Függőségek a tavaszi rendszerindítás 2.2-es verziójához

A Spring Boot 2.2-es verziójához adja hozzá a következő függőségeket az alkalmazás POM-fájljába.

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

## <a name="azure-spring-cloud-client-dependency"></a>Az Azure Spring Cloud ügyfélfüggősége

Az Azure Spring Cloud üzemelteti és kezeli a Tavaszi felhő összetevőit. Az összetevők közé tartozik a Spring Cloud Service Registry és a Spring Cloud Config Server. Az Azure Spring Cloud-ügyfélkönyvtárat is beillesztheti a függőségekbe, hogy lehetővé tegye a kommunikációt az Azure Spring Cloud szolgáltatáspéldányával.

Az alábbi táblázat a tavaszi rendszerindítást és a tavaszi felhőt használó alkalmazáshoz megfelelő Azure Spring Cloud-verziókat sorolja fel.

Tavaszi boot verzió | Tavaszi felhő verzió | Az Azure Spring Cloud verziója
---|---|---
2.1 | Greenwich.RELEASE | 2.1
2,2 | Hoxton.RELEASE | 2,2

A pom.xml fájlba az alábbi függőségek egyikét is bekell foglalnia. Válassza ki azt a függőséget, amelynek az Azure Spring Cloud verziója megegyezik a saját.

### <a name="dependency-for-azure-spring-cloud-version-21"></a>Függőség az Azure Spring Cloud 2.1-es verziójához

A Spring Boot 2.1-es verziójához adja hozzá a következő függőséget az alkalmazás POM-fájljába.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.1</version>
</dependency>
```

### <a name="dependency-for-azure-spring-cloud-version-22"></a>Függőség az Azure Spring Cloud 2.2-es verziójához

A Spring Boot 2.2-es verziójához adja hozzá a következő függőséget az alkalmazás POM-fájljába.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.2.0</version>
</dependency>
```

## <a name="other-required-dependencies"></a>Egyéb szükséges függőségek

Az Azure Spring Cloud beépített funkcióinak engedélyezéséhez az alkalmazásnak tartalmaznia kell a következő függőségeket. Ez a felvétel biztosítja, hogy az alkalmazás megfelelően konfigurálja magát az egyes összetevőkkel.

### <a name="enablediscoveryclient-annotation"></a>EnableDiscoveryClient jegyzet

Adja hozzá a következő jegyzetet az alkalmazás forráskódjához.
```java
@EnableDiscoveryClient
```
Például tekintse meg a piggymetrics alkalmazást a korábbi példákból:
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

### <a name="service-registry-dependency"></a>Szolgáltatás-beállításjegyzék-függőség

A felügyelt Azure Service Registry szolgáltatás `spring-cloud-starter-netflix-eureka-client` használatához adja meg a függőséget a pom.xml fájlban az itt látható módon:

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

A szolgáltatás beállításjegyzék-kiszolgáló végpontja automatikusan környezeti változóként kerül befecskendezésre az alkalmazással. Az alkalmazások regisztrálhatják magukat a Szolgáltatás beállításjegyzék-kiszolgálón, és felderíthetnek más függő mikroszolgáltatásokat.

### <a name="distributed-configuration-dependency"></a>Elosztott konfigurációs függőség

Az Elosztott konfiguráció engedélyezéséhez `spring-cloud-config-client` adja meg a következő függőséget a pom.xml fájl függőségek szakaszában:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Ne adja `spring.cloud.config.enabled=false` meg a bootstrap konfigurációban. Ellenkező esetben az alkalmazás nem működik a Config Server kiszolgálóval.

### <a name="metrics-dependency"></a>Metrikák függősége

A `spring-boot-starter-actuator` függőség felvétele a pom.xml fájl függőségek szakaszában az itt látható módon:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 Metrikák rendszeresen lekéri a JMX-végpontok. A metrikák at az Azure Portal használatával jelenítheti meg.

### <a name="distributed-tracing-dependency"></a>Elosztott nyomkövetési függőség

A következő `spring-cloud-starter-sleuth` `spring-cloud-starter-zipkin` és függőségek felvétele a pom.xml fájl függőségek szakaszában:

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

 Emellett engedélyeznie kell egy Azure Application Insights-példányt az Azure Spring Cloud-szolgáltatáspéldányhasználatával való munkához. Olvassa el az [elosztott nyomkövetésről szóló oktatóanyagot,](spring-cloud-tutorial-distributed-tracing.md) amelyből megtudhatja, hogyan használhatja az Application Insightsot az Azure Spring Cloud szolgáltatással.

## <a name="see-also"></a>Lásd még
* [Alkalmazásnaplók és -metrikák elemzése](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)
* [A Config Server beállítása](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-config-server)
* [Elosztott nyomkövetés használata az Azure Spring Cloud szolgáltatással](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
* [Tavaszi rövid útmutató](https://spring.io/quickstart)
* [Spring Boot dokumentáció](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>További lépések

Ebben a témakörben megtanulta, hogyan konfigurálhatja a Java Spring alkalmazást az Azure Spring Cloud üzembe helyezéséhez. A Config Server-példány beállításáról az alábbi cikkben olvashat.

> [!div class="nextstepaction"]
> [További információ a Config Server-példány beállításáról](spring-cloud-tutorial-config-server.md)

További minták érhetők el a GitHubon: [Azure Spring Cloud Samples.](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples)
