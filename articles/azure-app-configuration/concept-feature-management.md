---
title: A szolgáltatáskezelés ismertetése az Azure App Konfigurációja szolgáltatással
description: A funkciók be- és kikapcsolása az Azure App Configuration használatával
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 8227810c154078fc8424b2cadd373394d07e9730
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523730"
---
# <a name="feature-management-overview"></a>Szolgáltatáskezelés – áttekintés

Hagyományosan egy új alkalmazásfunkció szállítása az alkalmazás teljes átcsoportosítását igényli. Egy szolgáltatás tesztelése gyakran igényel több központi telepítések az alkalmazás.  Minden egyes központi telepítés módosíthatja a funkciót, vagy elérhetővé teheti a szolgáltatást különböző ügyfelek számára tesztelésre.  

A szolgáltatáskezelés egy modern szoftverfejlesztési gyakorlat, amely leválasztja a szolgáltatáskiadást a kód telepítéséről, és lehetővé teszi a gyors módosításokat a funkciók igény szerinti elérhetőségéhez. Ez használ egy technika úgynevezett *funkció jelzők* (más néven *szolgáltatás váltások*, *szolgáltatás kapcsolók*, és így tovább), hogy dinamikusan felügyelje a szolgáltatás életciklusát.

A szolgáltatáskezelés segítségével a fejlesztők a következő problémákat oldhatják meg:

* **Kódág-kezelés:** A szolgáltatásjelzők segítségével becsomagolja a jelenleg fejlesztés alatt álló új alkalmazásfunkciókat. Az ilyen funkciók alapértelmezés szerint "rejtettek". Biztonságosan elszállíthatja a funkciót, még akkor is, ha befejezetlen, és a termelés ben alvó marad. Ezzel a megközelítéssel, az úgynevezett *sötét központi telepítéssel,* minden egyes fejlesztési ciklus végén felszabadíthatja az összes kódot. A továbbiakban nem kell kódágakat fenntartani több fejlesztési ciklusban, mert egy adott funkció egynél több ciklust igényel.
* **Tesztelés éles környezetben:** A funkciójelzők segítségével korai hozzáférést biztosíthat az éles környezetúj funkcióihoz. Korlátozhatja például a csapattagok vagy a belső bétatesztelők hozzáférését. Ezek a felhasználók a teljes hűségű éles környezetben fogják tapasztalni a szimulált vagy részleges élmény helyett egy tesztkörnyezetben.
* **Repülés:** A szolgáltatásjelzők segítségével fokozatosan új funkciókat biztosíthat a végfelhasználók számára. Először a felhasználói populáció egy kis százalékát célozhatja meg, és ezt a százalékot fokozatosan növelheti az idő múlásával.
* **Azonnali kikapcsolókapcsoló:** A funkciójelzők az új funkciók felszabadítására szolgáló biztonsági hálót biztosítanak. Az alkalmazásfunkciókat kód újratelepítése nélkül is be- és kikapcsolhatja. Szükség esetén gyorsan letilthatja a szolgáltatást az alkalmazás újraépítése és újratelepítése nélkül.
* **Szelektív aktiválás:** A szolgáltatásjelzők segítségével szegmentálhatja a felhasználókat, és adott funkciókat juttathat el az egyes csoportokhoz. Előfordulhat, hogy olyan funkcióval rendelkezik, amely csak egy bizonyos webböngészőben működik. Megadhat egy jellemzőjelzőt, hogy csak az adott böngésző felhasználói láthassák és használhassák a funkciót. Ezzel a megközelítéssel később könnyedén kibonthatja a támogatott böngészőlistát anélkül, hogy bármilyen kódmódosítást kellene végrehajtania.

## <a name="basic-concepts"></a>Alapfogalmak

Íme néhány új kifejezés a szolgáltatáskezeléssel kapcsolatban:

* **Funkciójelző**: A jellemzőjelző olyan változó, amelynek bináris állapota *be-* vagy *kivan kapcsolva*. A szolgáltatásjelzőhöz tartozik egy kódblokk is. A szolgáltatásjelző állapota elindítja, hogy a kódblokk fut-e.
* **Szolgáltatáskezelő**: A szolgáltatáskezelő olyan alkalmazáscsomag, amely az alkalmazás összes szolgáltatásjelzőjének életciklusát kezeli. A szolgáltatáskezelő további funkciókat is biztosít, beleértve a szolgáltatásjelzők gyorsítótárazását és állapotuk frissítését.
* **Szűrő**: A szűrő a jellemzőjelző állapotának kiértékelésére szolgáló szabály. A lehetséges szűrők közé tartoznak a felhasználói csoportok, az eszköz- vagy böngészőtípusok, a földrajzi helyek és az időablakok.

A funkciókezelés hatékony megvalósítása legalább két, összehangoltan működő összetevőből áll:

* Szolgáltatásjelzőket használó alkalmazás.
* Egy külön tárház, amely tárolja a szolgáltatás jelzők és a jelenlegi állapotok.

## <a name="using-feature-flags-in-your-code"></a>Jellemzőjelzők használata a kódban

Az alkalmazás funkciójelzőinek megvalósításához szükséges alapvető minta egyszerű. A szolgáltatásjelző egy logikai állapotváltozó, amely a kódban lévő feltételes utasítást vezéreli:

```csharp
if (featureFlag) {
    // Run the following code
}
```

Beállíthatja `featureFlag` a statikus értéket.

```csharp
bool featureFlag = true;
```

A zászló állapotát bizonyos szabályok alapján értékelheti:

```csharp
bool featureFlag = isBetaUser();
```

A feltételes tanonc bármelyik állapotalkalmazás-viselkedés beállítására kiterjesztheti:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>Szolgáltatásjelző tárháza

A szolgáltatásjelzők hatékony használatához ki kell külsőítenie az alkalmazásban használt összes szolgáltatásjelzőt. Ez lehetővé teszi a szolgáltatásjelző-állapotok módosítását az alkalmazás módosítása és újratelepítése nélkül.

Az Azure App Configuration egy központi tárházat biztosít a szolgáltatásjelzők számára. Segítségével különböző típusú szolgáltatásjelzőket definiálhat, és gyorsan és magabiztosan kezelheti állapotukat. Ezután használhatja az alkalmazáskonfigurációs kódtárakat a különböző programozási nyelvi keretrendszerekhez, hogy könnyen elérhesse ezeket a szolgáltatásjelzőket az alkalmazásból.

[A szolgáltatásjelzők használata ASP.NET Core alkalmazásban](./use-feature-flags-dotnet-core.md) bemutatja, hogyan használja együtt a .NET Core alkalmazáskonfigurációs szolgáltató tárakat a ASP.NET webalkalmazás szolgáltatásjelzőinek megvalósításához.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Szolgáltatásjelzők hozzáadása ASP.NET Core webalkalmazáshoz](./quickstart-feature-flag-aspnet-core.md)  
