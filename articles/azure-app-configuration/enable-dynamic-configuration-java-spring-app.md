---
title: Dinamikus konfiguráció használata spring boot alkalmazásban
titleSuffix: Azure App Configuration
description: További információ a Spring Boot alkalmazások konfigurációs adatainak dinamikus frissítéséhez
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 3/5/2020
ms.author: lcozzens
ms.openlocfilehash: 37c832e3b6d1430da0b45558c9632f0486a7233b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79216762"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>Oktatóanyag: Dinamikus konfiguráció használata Java Spring alkalmazásban

Az Alkalmazáskonfigurációs tavaszi rendszerindítási ügyfélkönyvtár támogatja a konfigurációs beállítások igény szerinti frissítését anélkül, hogy egy alkalmazás újraindulna. Az ügyféltár gyorsítótárazza az egyes beállításokat, hogy elkerülje a konfigurációs tároló túl sok hívását. A frissítési művelet nem frissíti az értéket, amíg a gyorsítótárazott érték lejárt, még akkor is, ha az érték megváltozott a konfigurációs tárolóban. Az egyes kérelmek alapértelmezett lejárati ideje 30 másodperc. Szükség esetén felülbírálható.

Igény szerinti beállításokat igény szerint `AppConfigurationRefresh`a `refreshConfigurations()` metódus hívásával ellenőrizhet.

Azt is megteheti, `spring-cloud-azure-appconfiguration-config-web` hogy a csomagot, `spring-web` amely függőséget vesz igénybe az automatikus frissítés kezelésére.

## <a name="use-automated-refresh"></a>Automatikus frissítés használata

Az automatikus frissítés használatához kezdje egy, az alkalmazáskonfigurációt használó tavaszi rendszerindítási alkalmazással, például a létrehozott alkalmazással az [alkalmazáskonfiguráció tavaszi rendszerindítási rövid útmutatójának követésével.](quickstart-java-spring-app.md)

Ezután nyissa meg a *pom.xml* fájlt egy `<dependency>` `spring-cloud-azure-appconfiguration-config-web`szövegszerkesztőben, és adjon hozzá egy for-t.

**Tavaszi felhő 1.1.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.1.2</version>
</dependency>
```

**Tavaszi felhő 1.2.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.2.2</version>
</dependency>
```

Mentse a fájlt, majd építse össze és futtassa az alkalmazást a szokásos módon.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban engedélyezte, hogy a Tavaszi rendszerindítási alkalmazás dinamikusan frissítse a konfigurációs beállításokat az alkalmazáskonfigurációból. Ha meg szeretné tudni, hogyan használhatja az Azure felügyelt identitást az alkalmazáskonfigurációhoz való hozzáférés egyszerűsítéséhez, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Felügyelt identitásintegráció](./howto-integrate-azure-managed-service-identity.md)
