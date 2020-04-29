---
title: Dinamikus konfiguráció használata Spring boot-alkalmazásokban
titleSuffix: Azure App Configuration
description: Ismerje meg, hogyan frissítheti dinamikusan a Spring boot-alkalmazások konfigurációs információit
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 3/5/2020
ms.author: lcozzens
ms.openlocfilehash: 37c832e3b6d1430da0b45558c9632f0486a7233b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "79216762"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>Oktatóanyag: dinamikus konfiguráció használata Java Spring-alkalmazásokban

Az alkalmazás-konfiguráció rugó rendszerindítási ügyfélszoftvere támogatja az igény szerinti konfigurációs beállítások frissítését anélkül, hogy az alkalmazást újra kellene indítani. Az ügyféloldali kódtár gyorsítótárazza az egyes beállításokat, hogy ne legyen túl sok hívás a konfigurációs tárolóba. A frissítési művelet nem frissíti az értéket addig, amíg a gyorsítótárazott érték lejár, még akkor is, ha az érték megváltozott a konfigurációs tárolóban. Az egyes kérések alapértelmezett lejárati ideje 30 másodperc. Szükség esetén felül lehet bírálni.

Az igény szerinti frissített beállításokat a hívás `AppConfigurationRefresh` `refreshConfigurations()` metódusa alapján tekintheti meg.

Azt is megteheti, `spring-cloud-azure-appconfiguration-config-web` `spring-web` hogy a csomagot használja, amely függőséget igényel az automatikus frissítés kezeléséhez.

## <a name="use-automated-refresh"></a>Automatikus frissítés használata

Az automatikus frissítés használatához kezdjen el egy olyan Spring boot-alkalmazással, amely az alkalmazás konfigurációját használja, mint például az alkalmazás [konfigurációját követő Spring boot](quickstart-java-spring-app.md)rövid útmutató alapján létrehozott alkalmazás.

Ezután nyissa meg a *Pom. XML* fájlt egy szövegszerkesztőben, és adjon hozzá `<dependency>` egy `spring-cloud-azure-appconfiguration-config-web`-t.

**Spring Cloud 1.1. x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.1.2</version>
</dependency>
```

**Spring Cloud 1.2. x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.2.2</version>
</dependency>
```

Mentse a fájlt, majd a szokásos módon hozza létre és futtassa az alkalmazást.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban engedélyezte a Spring boot-alkalmazást, hogy dinamikusan frissítse a konfigurációs beállításokat az alkalmazás konfigurációjában. Ha meg szeretné tudni, hogyan használható az Azure felügyelt identitása az alkalmazás-konfigurációhoz való hozzáférés egyszerűsítéséhez, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Felügyelt identitások integrációja](./howto-integrate-azure-managed-service-identity.md)
