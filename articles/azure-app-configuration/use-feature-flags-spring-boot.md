---
title: Oktatóanyag a tavaszi rendszerindítási alkalmazások funkciójelzőinek használatához - Azure App Configuration | Microsoft dokumentumok
description: Ebben az oktatóanyagban megtudhatja, hogyan valósíthatja meg a funkciójelzőket a tavaszi rendszerindítási alkalmazásokban.
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
ms.openlocfilehash: d519ac44d617f725aa9b3d3f11671122bd9477bc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78944326"
---
# <a name="tutorial-use-feature-flags-in-a-spring-boot-app"></a>Oktatóanyag: Szolgáltatásjelzők használata a Spring Boot alkalmazásban

A tavaszi rendszerindító alapszolgáltatás-kezelő kódtárak támogatják a szolgáltatásjelzők megvalósítását a tavaszi rendszerindítási alkalmazásban. Ezek a tárak lehetővé teszik, hogy deklaratív módon hozzá szolgáltatásjelzők a kódot.

A Szolgáltatáskezelő kódtárak is kezelheti a funkciók jelzőéletciklusát a színfalak mögött. Például a könyvtárak frissítik és gyorsítótárazási jelző állapotai, vagy garantálja a zászló állapot nem módosítható kérés hívás közben. Emellett a Spring Boot könyvtár integrációkat is kínál, beleértve az MVC vezérlő műveleteket, útvonalakat és köztes szoftvereket.

A [Kiegészítő funkciójelzők a tavaszi rendszerindítási alkalmazáshoz rövid útmutató](./quickstart-feature-flag-spring-boot.md) ban számos lehetőség van a szolgáltatásjelzők hozzáadására a tavaszi rendszerindítási alkalmazásban. Ez az oktatóanyag részletesebben ismerteti ezeket a módszereket.

Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
> * A szolgáltatás elérhetőségének szabályozásához adja hozzá a szolgáltatásjelzőket az alkalmazás kulcsfontosságú részeihez.
> * Integrálja az alkalmazáskonfigurációval, amikor a funkciójelzők kezelésére használja.

## <a name="set-up-feature-management"></a>Szolgáltatáskezelés beállítása

A Spring Boot `FeatureManager` szolgáltatáskezelő funkciójelzőket kap a keretrendszer natív konfigurációs rendszeréből. Ennek eredményeképpen megadhatja az alkalmazás szolgáltatásjelzőit bármely olyan konfigurációs forrás használatával, amelyet a Spring Boot támogat, beleértve a helyi *bootstrap.yml* fájlt vagy környezeti változókat. `FeatureManager`függőségi injekcióra támaszkodik. A szolgáltatásfelügyeleti szolgáltatásokat szabványos konvenciók használatával regisztrálhatja:

```java
private FeatureManager featureManager;

public HelloController(FeatureManager featureManager) {
    this.featureManager = featureManager;
}
```

Azt javasoljuk, hogy tartsa a szolgáltatásjelzők az alkalmazáson kívül, és kezelje őket külön-külön. Ezzel bármikor módosíthatja a jelzőállapotokat, és azonnal életbe léptetheti ezeket a módosításokat az alkalmazásban. Az Alkalmazáskonfiguráció központosított helyet biztosít az összes funkciójelző rendszerezéséhez és vezérléséhez egy dedikált portál felhasználói felületén keresztül. Az Alkalmazáskonfiguráció közvetlenül a tavaszi rendszerindítási ügyfélkódtárakon keresztül juttatja el a jelzőket az alkalmazáshoz.

A spring boot alkalmazás alkalmazáskonfigurációhoz való csatlakoztatásának legegyszerűbb módja a konfigurációs szolgáltatón keresztül történik:

### <a name="spring-cloud-11x"></a>Tavaszi felhő 1.1.x

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-feature-management-web</artifactId>
    <version>1.1.2</version>
</dependency>
```

### <a name="spring-cloud-12x"></a>Tavaszi felhő 1.2.x

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-feature-management-web</artifactId>
    <version>1.2.2</version>
</dependency>
```

## <a name="feature-flag-declaration"></a>Szolgáltatásjelző deklarációja

Minden jellemzőjelzőnek két része van: egy név ből és egy vagy több szűrőből álló listából, amely `True`annak megállapítására szolgál, hogy egy szolgáltatás állapota be van-e *kapcsolva* (azaz ha az értéke). A szűrő azt a használati esetet határozza meg, hogy mikor kell egy szolgáltatást bekapcsolni.

Ha egy szolgáltatásjelző több szűrővel rendelkezik, a szűrőlista sorrendben halad át, amíg az egyik szűrő meg nem határozza, hogy engedélyezni kell a szolgáltatást. Ezen a ponton a szolgáltatásjelző be van *kapcsolva,* és a fennmaradó szűrőeredmények kimaradnak. Ha nincs szűrő, akkor a szolgáltatást engedélyezni kell, akkor a szolgáltatásjelző ki van *kapcsolva.*

A szolgáltatáskezelő támogatja *az application.yml* fájlt a szolgáltatásjelzők konfigurációs forrásaként. A következő példa bemutatja, hogyan állíthat be szolgáltatásjelzőket egy YAML-fájlban:

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

A program `feature-management` konvenció szerint a jelen YML-dokumentum szakaszát használja a szolgáltatásjelző-beállításokhoz. Az előző példában három jellemzőjelző látható `EnabledFor` a tulajdonságban definiált szűrőkkel:

* `feature-a`be van *kapcsolva.*
* `feature-b`ki van *kapcsolva.*
* `feature-c`tulajdonsággal elnevezett `Percentage` szűrőt `parameters` ad meg. `Percentage`egy konfigurálható szűrő. Ebben a `Percentage` példában 50 százalékos valószínűséget `feature-c` ad meg a *jelzőnek.*

## <a name="feature-flag-checks"></a>Jellemzőjelző-ellenőrzések

A szolgáltatáskezelés alapvető mintája az, hogy először ellenőrizze, hogy egy jellemzőjelző be *van-e*állítva . Ha igen, a szolgáltatáskezelő ezután futtatja a funkció ban található műveleteket. Példa:

```java
private FeatureManager featureManager;
...
if (featureManager.isEnabledAsync("feature-a").block()) {
    // Run the following code
}
```

## <a name="dependency-injection"></a>Függőséginjektálás

A Spring Boot,elérheti a `FeatureManager` szolgáltatás kezelő függőség injekció:

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

## <a name="controller-actions"></a>Vezérlő műveletek

Az MVC-vezérlőkben az `@FeatureGate` attribútum segítségével szabályozhatja, hogy egy adott művelet engedélyezve van-e. A `Index` következő `feature-a` műveletnek be kell *kapcsolva* a futtatáshoz:

```java
@GetMapping("/")
@FeatureGate(feature = "feature-a")
public String index(Model model) {
    ...
}
```

Ha egy MVC-vezérlő vagy művelet le van tiltva, mert a vezérlő funkciójelző ki van *kapcsolva,* a rendszer regisztrált `IDisabledFeaturesHandler` felületet hív meg. Az `IDisabledFeaturesHandler` alapértelmezett felület egy 404-es állapotkódot ad vissza az ügyfélnek választörzs nélkül.

## <a name="mvc-filters"></a>MVC szűrők

Az MVC-szűrőket beállíthatja úgy, hogy a szolgáltatásjelző állapota alapján aktiválódjanak. A következő kód hozzáad `FeatureFlagFilter`egy MVC szűrőt, amelynek neve . Ez a szűrő csak akkor aktiválódik `feature-a` az MVC-folyamaton belül, ha engedélyezve van.

```java
@Component
public class FeatureFlagFilter implements Filter {

    @Autowired
    private FeatureManager featureManager;

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        if(!featureManager.isEnabledAsync("feature-a").block()) {
            chain.doFilter(request, response);
            return;
        }
        ...
        chain.doFilter(request, response);
    }
}
```

## <a name="routes"></a>Útvonalak

A jellemzőjelzők segítségével átirányíthatja az útvonalakat. A következő kód átirányítja `feature-a` a felhasználót engedélyezve van:

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

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan valósíthat meg funkciójelzőket a tavaszi rendszerindítási alkalmazásban a `spring-cloud-azure-feature-management-web` könyvtárak használatával. A tavaszi rendszerindítás és az alkalmazáskonfiguráció szolgáltatáskezelési támogatásáról az alábbi forrásokban talál további információt:

* [Tavaszi rendszerindítási funkció jelzőmintakódja](/azure/azure-app-configuration/quickstart-feature-flag-spring-boot)
* [Funkciójelölők kezelése](./manage-feature-flags.md)
