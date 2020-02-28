---
title: Oktatóanyag a Feature Flags használatához egy Spring boot-alkalmazásban – Azure app Configuration | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan valósíthatók meg a szolgáltatás-jelzők a Spring boot-alkalmazásokban.
services: azure-app-configuration
documentationcenter: ''
author: mrm9084
manager: zhenlwa
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: java
ms.topic: tutorial
ms.date: 09/26/2019
ms.author: mametcal
ms.custom: mvc
ms.openlocfilehash: 090ede85301f9e7aff14394c8fb5c7d558d98dd4
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77656024"
---
# <a name="tutorial-use-feature-flags-in-a-spring-boot-app"></a>Oktatóanyag: funkció-jelzők használata Spring boot-alkalmazásokban

A tavaszi rendszerindítási alapszolgáltatások kezelési kódtárai támogatják a szolgáltatás-jelzők megvalósítását egy Spring boot-alkalmazásban. Ezek a kódtárak lehetővé teszik a funkció-jelzők deklaratív hozzáadását a kódhoz.

A szolgáltatás-felügyeleti kódtárak a funkciók jelző életciklusait is kezelik a színfalak mögött. Például a tárak frissítési és gyorsítótár-jelző állapota, vagy ha egy kérelem hívásakor a jelző állapot nem változtatható meg. Emellett a Spring boot Library integrációkat is kínál, beleértve az MVC vezérlő műveleteit, az útvonalakat és a köztes adatforrásokat.

A [Spring boot-alkalmazás funkcióinak hozzáadása](./quickstart-feature-flag-spring-boot.md) a Spring boot-alkalmazásban számos módszert mutat be a funkció-jelzők hozzáadására. Ez az oktatóanyag részletesen ismerteti ezeket a módszereket.

Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
> * A szolgáltatások rendelkezésre állásának szabályozásához vegyen fel szolgáltatás-jelzőket az alkalmazás legfontosabb részeibe.
> * Integrálhatja az alkalmazás konfigurációját, ha a funkció-jelzők kezelésére használja.

## <a name="set-up-feature-management"></a>A szolgáltatások kezelésének beállítása

A Spring boot Feature Manager `FeatureManager` lekéri a keretrendszer natív konfigurációs rendszerének funkcióit. Ennek eredményeképpen meghatározhatja az alkalmazás funkciójának jelzőit bármely olyan konfigurációs forrás használatával, amelyet a Spring boot támogat, beleértve a helyi *bootstrap. YML* fájl-vagy környezeti változókat is. a `FeatureManager` függőségi injektálásra támaszkodik. A szolgáltatás-felügyeleti szolgáltatásokat szabványos konvenciók használatával regisztrálhatja:

```java
private FeatureManager featureManager;

public HelloController(FeatureManager featureManager) {
    this.featureManager = featureManager;
}
```

Javasoljuk, hogy az alkalmazáson kívül tartsa meg a szolgáltatás jelölőit, és ezeket külön kell kezelnie. Ezzel a beállítással bármikor módosíthatja a jelző állapotokat, és ezek a módosítások azonnal érvénybe lépnek az alkalmazásban. Az alkalmazás konfigurációja központosított helyet biztosít az összes funkció jelzőjének egy dedikált portál felhasználói felületen való rendszerezéséhez és szabályozásához. Az alkalmazás konfigurációja emellett közvetlenül a Spring boot-ügyfél könyvtárain keresztül továbbítja az alkalmazáshoz tartozó jelzőket.

A Spring boot-alkalmazásnak az alkalmazás-konfigurációhoz való összekapcsolásának legegyszerűbb módja a konfigurációs szolgáltatón keresztül:

### <a name="spring-cloud-11x"></a>Spring Cloud 1.1. x

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-feature-management-web</artifactId>
    <version>1.1.1</version>
</dependency>
```

### <a name="spring-cloud-12x"></a>Spring Cloud 1.2. x

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-feature-management-web</artifactId>
    <version>1.2.1</version>
</dependency>
```

## <a name="feature-flag-declaration"></a>Funkció jelző deklarációja

Minden egyes szolgáltatás jelölője két részből áll: egy vagy több szűrőből áll, amelyek segítségével kiértékelheti, hogy a szolgáltatás állapota be van-e *kapcsolva* (azaz ha az értéke `True`). A szűrők a használati esetet határozzák meg, ha egy szolgáltatás bekapcsolására van lehetőség.

Ha egy szolgáltatás jelölője több szűrővel rendelkezik, a rendszer átadja a szűrőlisták sorrendjét, amíg az egyik szűrő nem határozza meg, hogy a szolgáltatást engedélyezni kell. Ekkor a funkció jelzője *be van kapcsolva*, és a rendszer kihagyja a többi szűrő eredményét. Ha nincs szűrő azt jelzi, hogy a funkciót engedélyezni kell, a szolgáltatás jelzője *ki van kapcsolva*.

A Feature Manager támogatja az *Application. YML* konfigurációs forrásként való használatát a szolgáltatások jelzői számára. Az alábbi példa bemutatja, hogyan állíthatja be a szolgáltatás jelzőit egy YAML-fájlban:

```yml
feature-management:
  feature-set:
    feature-a: true
    feature-b: false
    feature-c:
      enabled-for:
        -
          name: Percentage
          parameters:
            value: 50
```

Az egyezmény szerint a YML-dokumentum `feature-management` szakasza a szolgáltatás-jelölő beállításaihoz használatos. Az előző példában három funkció-jelző látható a `EnabledFor` tulajdonságban definiált szűrőkkel:

* `feature-a` *bekapcsolva*.
* `feature-b` *ki van kapcsolva*.
* `feature-c` a `Percentage` nevű szűrőt adja meg `parameters` tulajdonsággal. `Percentage` konfigurálható szűrő. Ebben a példában a `Percentage` 50 százalékos valószínűséget *ad meg a*`feature-c` jelzőhöz.

## <a name="feature-flag-checks"></a>Szolgáltatás-jelző ellenőrzése

A szolgáltatások felügyeletének alapszintű mintája először ellenőrizze, hogy be van *-e állítva*a szolgáltatás jelölője. Ebben az esetben a Feature Manager ezután futtatja a funkció által tartalmazott műveleteket. Például:

```java
private FeatureManager featureManager;
...
if (featureManager.isEnabledAsync("feature-a"))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Függőséginjektálás

A Spring boot használatával a szolgáltatás-kezelő `FeatureManager` a függőségek befecskendezésével érheti el:

```java
@Controller
@ConfigurationProperties("controller")
public class HomeController {
    private FeatureManager featureManager;

    public HomeController(FeatureManager featureManager) {
        this.featureManager = featureManager;
    }
}
```

## <a name="controller-actions"></a>Vezérlő műveletei

Az MVC-vezérlőkben a `@FeatureGate` attribútum használatával szabályozhatja, hogy egy adott művelet engedélyezve van-e. A következő `Index` művelet végrehajtásához `feature-a` kell lennie *a* futtatásához:

```java
@GetMapping("/")
@FeatureGate(feature = "feature-a")
public String index(Model model) {
    ...
}
```

Ha egy MVC vezérlő vagy művelet le van tiltva, mert a vezérlő funkció jelzője *ki van kapcsolva*, a rendszer regisztrált `IDisabledFeaturesHandler` felületet hív meg. Az alapértelmezett `IDisabledFeaturesHandler` felület 404 állapotkódot ad vissza az ügyfélnek, és nincs válasz törzse.

## <a name="mvc-filters"></a>MVC-szűrők

Az MVC-szűrőket beállíthatja úgy, hogy azok a szolgáltatás jelzőjének állapota alapján legyenek aktiválva. A következő kód egy `FeatureFlagFilter`nevű MVC-szűrőt hoz létre. Ez a szűrő csak akkor aktiválódik az MVC-folyamaton belül, ha `feature-a` engedélyezve van.

```java
@Component
public class FeatureFlagFilter implements Filter {

    @Autowired
    private FeatureManager featureManager;

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        if(!featureManager.isEnabled("feature-a")) {
            chain.doFilter(request, response);
            return;
        }
        ...
        chain.doFilter(request, response);
    }
}
```

## <a name="routes"></a>Útvonalak

Az útvonalak átirányításához használhatja a szolgáltatás jelzőit. A következő kód átirányítja a felhasználót `feature-a`ről, ha engedélyezve van:

```java
@GetMapping("/redirect")
@FeatureGate(feature = "feature-a", fallback = "/getOldFeature")
public String getNewFeature() {
    // Some New Code
}

@GetMapping("/getOldFeature")
public String getOldFeature() {
    // Some New Code
}
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan implementálhatja a szolgáltatás-jelzőket a Spring boot-alkalmazásban a `spring-cloud-azure-feature-management-web` kódtárak használatával. A Spring boot és az alkalmazások konfigurációjának szolgáltatás-kezelési támogatásával kapcsolatos további információkért tekintse meg a következő forrásokat:

* [Tavaszi rendszerindítási funkció jelölője – mintakód](/azure/azure-app-configuration/quickstart-feature-flag-spring-boot)
* [Funkciójelölők kezelése](./manage-feature-flags.md)
