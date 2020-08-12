---
title: Dinamikus konfiguráció használata Spring boot-alkalmazásokban
titleSuffix: Azure App Configuration
description: Ismerje meg, hogyan frissítheti dinamikusan a Spring boot-alkalmazások konfigurációs információit
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 3/5/2020
ms.custom: devx-track-java
ms.author: lcozzens
ms.openlocfilehash: 653fcb6f6590fd503a97800ec8196025cf14a3b9
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121561"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>Oktatóanyag: dinamikus konfiguráció használata Java Spring-alkalmazásokban

Az alkalmazás-konfiguráció rugó rendszerindítási ügyfélszoftvere támogatja az igény szerinti konfigurációs beállítások frissítését anélkül, hogy az alkalmazást újra kellene indítani. Az ügyféloldali kódtár gyorsítótárazza az egyes beállításokat, hogy ne legyen túl sok hívás a konfigurációs tárolóba. A frissítési művelet nem frissíti az értéket addig, amíg a gyorsítótárazott érték lejár, még akkor is, ha az érték megváltozott a konfigurációs tárolóban. Az egyes kérések alapértelmezett lejárati ideje 30 másodperc. Szükség esetén felül lehet bírálni.

Az igény szerinti frissített beállításokat a hívás metódusa alapján tekintheti meg `AppConfigurationRefresh` `refreshConfigurations()` .

Azt is megteheti, `spring-cloud-azure-appconfiguration-config-web` hogy a csomagot használja, amely függőséget igényel `spring-web` az automatikus frissítés kezeléséhez.

## <a name="use-automated-refresh"></a>Automatikus frissítés használata

Az automatikus frissítés használatához kezdjen el egy olyan Spring boot-alkalmazással, amely az alkalmazás konfigurációját használja, mint például az alkalmazás [konfigurációját követő Spring boot](quickstart-java-spring-app.md)rövid útmutató alapján létrehozott alkalmazás.

Ezután nyissa meg a *pom.xml* fájlt egy szövegszerkesztőben, és adjon hozzá egy-t `<dependency>` `spring-cloud-azure-appconfiguration-config-web` .

**Spring Cloud 1.1. x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.1.5</version>
</dependency>
```

**Spring Cloud 1.2. x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.2.7</version>
</dependency>
```

## <a name="run-and-test-the-app-locally"></a>Az alkalmazás helyi futtatása és tesztelése

1. Állítsa össze a Spring Boot-alkalmazást a Mavennel, és futtassa.

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Nyisson meg egy böngészőablakot, és lépjen a következő URL-címre: `http://localhost:8080` .  Ekkor megjelenik a kulcshoz tartozó üzenet. 

    Az alkalmazás teszteléséhez a *curl* is használható, például: 
    
    ```cmd
    curl -X GET http://localhost:8080/
    ```

1. A dinamikus konfiguráció teszteléséhez nyissa meg az alkalmazáshoz társított Azure app Configuration Portalt. Válassza a **Configuration Explorer**lehetőséget, és frissítse a megjelenített kulcs értékét, például:
    | Kulcs | Érték |
    |---|---|
    | alkalmazás/config. Message | Hello – frissítve |

1. Az új üzenet megjelenítéséhez frissítse a böngésző lapját.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban engedélyezte a Spring boot-alkalmazást, hogy dinamikusan frissítse a konfigurációs beállításokat az alkalmazás konfigurációjában. Ha meg szeretné tudni, hogyan használható az Azure felügyelt identitása az alkalmazás-konfigurációhoz való hozzáférés egyszerűsítéséhez, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Felügyelt identitások integrációja](./howto-integrate-azure-managed-service-identity.md)
