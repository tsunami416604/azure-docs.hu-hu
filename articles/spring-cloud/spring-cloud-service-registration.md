---
title: Szolgáltatásbeállítás-nyilvántartás és felderítés automatizálása
description: A szolgáltatásfelderítés és -regisztráció automatizálása a Spring Cloud Service Registry használatával
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: brendm
ms.openlocfilehash: 6c217096f0ba4200f49bd1fd8056768a6f6f6dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278860"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>Fedezze fel és regisztrálja tavaszi felhőszolgáltatásait

A szolgáltatásfelderítés a mikroszolgáltatás-alapú architektúra kulcsfontosságú követelménye.  Az egyes ügyfelek manuális konfigurálása időt vesz igénybe, és bevezeti az emberi hiba lehetőségét.  Az Azure Spring Cloud Service Registry megoldja ezt a problémát.  Konfigurálás után a Szolgáltatás beállításjegyzék-kiszolgáló ellenőrzi az alkalmazás mikroszolgáltatásainak szolgáltatásregisztrációját és felderítését. A Szolgáltatás beállításjegyzék-kiszolgálója nyilvántartást vezet a telepített mikroszolgáltatásokról, lehetővé teszi az ügyféloldali terheléselosztást, és leválasztja a szolgáltatókat az ügyfelektől anélkül, hogy dns-re támaszkodna.

## <a name="register-your-application-using-spring-cloud-service-registry"></a>Regisztrálja alkalmazását a Spring Cloud Service Registry használatával

Ahhoz, hogy az alkalmazás kezelni tudja a szolgáltatás regisztrációját és felderítését a Spring Cloud Service Registry használatával, számos függőséget kell tartalmaznia az alkalmazás *pom.xml* fájljában.
A tavaszi *felhő-starter-netflix-eureka-kliens* és a *spring-cloud-starter-azure-spring-cloud-client* függőségeit is tartalmazza a *pom.xml fájlba.*

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
    </dependency>
```

## <a name="update-the-top-level-class"></a>A legfelső szintű osztály frissítése

Végül egy jegyzetet adunk az alkalmazás felső szintű osztályához

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

A spring cloud service registry kiszolgáló végpontja az alkalmazás környezeti változóként lesz injektálva.  A mikroszolgáltatások most már regisztrálhatják magukat a Szolgáltatás beállításjegyzék-kiszolgálón, és felderíthetnek más függő mikroszolgáltatásokat.

Vegye figyelembe, hogy eltarthat néhány percig, amíg a módosítások propagálnak a kiszolgálóról az összes mikroszolgáltatásra.
