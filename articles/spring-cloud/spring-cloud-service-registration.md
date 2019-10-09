---
title: A szolgáltatás beállításjegyzékének és felderítésének automatizálása
description: Ismerje meg, hogyan automatizálható a szolgáltatás-felderítés és-regisztráció a Spring Cloud Service beállításjegyzék használatával
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: jeconnoc
ms.openlocfilehash: 72327e116e498ce0f6881a5c585a08e56c8bf8c2
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038742"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>A Spring Cloud Services felderítése és regisztrálása

A Service Discovery kulcsfontosságú követelmény a Service-alapú architektúrák számára.  Az egyes ügyfelek manuális beállítása időt vesz igénybe, és bevezeti az emberi hiba lehetőségét.  Az Azure Spring Cloud Service beállításjegyzéke megoldja ezt a problémát.  A konfigurálást követően a szolgáltatás-regisztrációs kiszolgáló a szolgáltatás regisztrálását és felderítését fogja szabályozni az alkalmazáshoz tartozó szolgáltatásokhoz. A szolgáltatás beállításjegyzék-kiszolgálója karbantartja az üzembe helyezett szolgáltatások beállításjegyzékét, lehetővé teszi az ügyféloldali terheléselosztást, és leválasztja a szolgáltatókat az ügyfelektől a DNS-re való támaszkodás nélkül.

## <a name="register-your-application-using-spring-cloud-service-registry"></a>Alkalmazás regisztrálása a Spring Cloud Service Registry használatával

Ahhoz, hogy az alkalmazás kezelni tudja a szolgáltatás regisztrációját és a felderítést a Spring Cloud Service beállításjegyzékével, számos függőséget fel kell venni az alkalmazás *Pom. XML* fájljába.

A kezdéshez vegyen fel egy pillanatkép-tárházat a *Pom. XML* *adattár* szakaszába.

```xml
    <repositories>
        <repository>
            <id>nexus-snapshots</id>
            <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
    </repositories>
```

## <a name="include-dependencies"></a>Függőségek belefoglalása

A következő lépés a *Spring-Cloud-Starter-Netflix-Eureka-Client* és a *Spring-Cloud-Starter-Azure-Spring-Cloud-Client* és a *Pom. XML* közötti függőségek belefoglalása.

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0-SNAPSHOT</version>
    </dependency>
```

## <a name="update-the-top-level-class"></a>A legfelső szintű osztály frissítése

Végezetül hozzáadunk egy jegyzetet az alkalmazás legfelső szintű osztályához

 ```java
    package foo.bar;

    import org.springframework.boot.SpringApplication;
    import org.springframework.cloud.client.SpringCloudApplication;

    @SpringCloudApplication
    public class DemoApplication {
        public static void main(String... args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
 ```

A Spring Cloud Service beállításjegyzék-kiszolgáló végpontja környezeti változóként lesz befecskendezve az alkalmazásban.  A-szolgáltatások mostantól képesek regisztrálni magukat a szolgáltatás beállításjegyzék-kiszolgálójával, és felfedezhetik a többi függő szolgáltatást is.

Vegye figyelembe, hogy a módosítások a kiszolgálóról az összes-szolgáltatásra történő propagálására is eltarthat néhány percig.
