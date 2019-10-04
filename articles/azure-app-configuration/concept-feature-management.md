---
title: Az Azure App szolgáltatás kezelési |} A Microsoft Docs
description: Hogyan alkalmazások konfigurálása az Azure segítségével kapcsolhatja be és ki az igény szerinti szolgáltatások alkalmazás áttekintése.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 04/19/2019
ms.author: yegu
ms.openlocfilehash: 46f39e87e4e4cf115cbc1fceeabf0dab38fade28
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393365"
---
# <a name="feature-management-overview"></a>A szolgáltatás áttekintése

Hagyományosan szállítási egy új application szolgáltatás szükséges a teljes újbóli üzembe helyezés magának az alkalmazásnak. Szolgáltatás tesztelése, valószínűleg telepítenie kell az alkalmazás hány alkalommal, amikor láthatóvá válik a szolgáltatás vagy akik lekérdezi, ha meg szeretné.

A szolgáltatás egy modern szoftverfejlesztői eljárás, amely a kód telepítése funkciókat tartalmazó kiadások leválasztja, és lehetővé teszi, hogy a szolgáltatás rendelkezésre állása, igény szerinti gyorsmódosítások. Nevezett technika használ *jelzők funkció* (más néven *be-vagy kikapcsolja a szolgáltatás*, *a szolgáltatási kapcsolók*, és így tovább) dinamikusan felügyelheti egy szolgáltatás-életciklusának.

Felügyeleti szolgáltatás segítségével a fejlesztők a következő problémák kezelésére:

* **Ág forráskódkezelést**: A szolgáltatás jelzők gyakran használják burkolása alkalmazás funkciókat fejlesztés alatt áll. Ilyen funkciók "rejtett" alapértelmezés szerint. Biztonságosan el a szolgáltatást, annak ellenére, hogy rendelkezik befejezetlen, és éles környezetben inaktív marad. Ez a módszer használatával nevű *sötét telepítési*, fel lehet szabadítani valamennyi a kód minden fejlesztési szakaszban végén. Már nincs szüksége egy szolgáltatás véve a több ciklust hajtható végre, mert több ciklusok bármilyen kód ág karbantartására akár.
* **Éles környezetben végzett tesztelés**: A szolgáltatás jelzők segítségével éles környezetben új funkció korai hozzáférést. Például korlátozhatja a hozzáférést csak a csoport tagjai, vagy belső bétatesztelőknek. Ezek a felhasználók a teljes hűségű éles élmény helyett egy szimulált vagy részleges felület tesztelési környezetben fog kapni.
* **Fokozatos bevezetéssel**: A szolgáltatás jelzők segítségével fokozatos bevezetése új funkciókat a végfelhasználók számára. Egy kis csoportja a felhasználói körhöz először célozza, és százalékosan fokozatosan növelje után végrehajtása magabiztosabb révén az idő múlásával.
* **Azonnali kill kapcsoló**: Szolgáltatás jelzők egy rejlő hálóként ad ki az új funkciókat biztosítanak. Velük könnyen kapcsolhatja alkalmazás funkciók be- és kikapcsolása. Szükség esetén gyorsan letiltható egy funkció újraépítése és az alkalmazás újbóli telepítése nélkül.
* **Szelektív aktiválási**: A legtöbb funkciót jelzők csak addig, amíg a kapcsolódó funkciók sikeresen kiadott létezik, míg egyes hosszú ideig is utolsó. A szolgáltatás jelzők segítségével oszthatja a felhasználóit és a egy meghatározott készletének szolgáltatások továbbítására az egyes csoportok. Például előfordulhat, hogy rendelkezik olyan szolgáltatás, amely csak bizonyos webböngésző működik. A szolgáltatás azt a jelzőt határozhatja meg, hogy csak a felhasználók az adott böngésző is talál, és a funkció használatához. Ezzel a módszerrel könnyen kibontásával, a támogatott böngésző listában később anélkül, hogy kódot kellene.

## <a name="basic-concepts"></a>Alapfogalmak

Az alábbiakban néhány új használati funkció kezelésével kapcsolatos:

* **Jelző funkció**: A szolgáltatás azt a jelzőt egy változót egy bináris állapotával *a* vagy *ki*. A funkció jelölője is rendelkezik az ahhoz tartozó kódot letiltása. A funkció jelölője állapotát elindítja a kódblokk vagy nem fut-e.
* **A szolgáltatás manager**: A szolgáltatás manager olyan alkalmazáscsomagot jelent, amely kezeli az alkalmazások minden funkció jelző életciklusát. A szolgáltatás-kezelő általában biztosít további funkciókat, például a szolgáltatás jelzők gyorsítótárazását, és azok állapota frissítése.
* **Szűrő**: Szűrő egy funkció jelölője állapotának értékeléséhez szabály. Felhasználói csoport, egy eszköz vagy a böngésző típusa, földrajzi hely és a egy olyan időkeretet példák az összes szűrő jelenthetik.

A szolgáltatás felügyeleti hatékony megvalósítása vírusvédelmet használatához legalább két összetevőből áll:

* Olyan alkalmazás, amely lehetővé teszi a funkció jelzők használja.
* Egy külön adattár, amely tárolja a szolgáltatás jelzők és azok aktuális állapota.

Hogyan ezeket az összetevőket használják a következő példákban látható.

## <a name="feature-flag-usage-in-code"></a>Funkciók jelző használata a kódban

Az egyszerű szolgáltatás jelzők megvalósítása az alkalmazás egyik egyszerű. A szolgáltatás azt a jelzőt használt logikai állapot változóként felfoghatók egy `if` feltételes utasítás a kódban:

```csharp
if (featureFlag) {
    // Run the following code
}
```

Ebben az esetben ha `featureFlag` értékre van állítva `True`, a mellékelt kódblokk végrehajtott; egyéb, akkor a rendszer kihagyta. Beállíthatja, hogy értékét `featureFlag` statikusan, ahogy az alábbi példakód:

```csharp
bool featureFlag = true;
```

A jelző állapot bizonyos szabályok alapján kiértékelheti is:

```csharp
bool featureFlag = isBetaUser();
```

Egy némileg összetettebb funkció jelző mintát is tartalmaz egy `else` utasítást is:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

Azonban módosíthatja ezt a viselkedést az alapvető mintája. [A szolgáltatás jelzők használata az ASP.NET Core-alkalmazás](./use-feature-flags-dotnet-core.md) bemutatja egy egyszerű kódmintát a szabványosításával előnyeit. Példa:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
}

if (!featureFlag) {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>A szolgáltatás jelző tárház

A szolgáltatás jelzők hatékony használatához kell az alkalmazások használni minden funkció jelző externalize. Ez a megközelítés lehetővé teszi, hogy jelző Szolgáltatásállapotok módosíthatja a módosításával, és maga az alkalmazás újbóli telepítése nélkül.

Az Azure App konfigurálása funkció jelzők központi adattára tervezték. Használhatja a szolgáltatás jelzők különböző típusú és azok állapota módosítására, gyors és magabiztos kiépítésében. Ezután használhatja az Alkalmazáskonfigurációt kódtárak különböző programozási nyelv keretrendszerek is egyszerűen hozzáférhet a szolgáltatás jelző az alkalmazásból.

[A szolgáltatás jelzők használata az ASP.NET Core-alkalmazás](./use-feature-flags-dotnet-core.md) bemutatja, hogyan a .NET Core-alkalmazás konfigurációs szolgáltató és a szolgáltatás felügyeleti könyvtárak együttesen arra használják az ASP.NET-alkalmazás jelzőket a szolgáltatás megvalósítása.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A szolgáltatás jelzők hozzáadása az ASP.NET Core-webalkalmazás](./quickstart-feature-flag-aspnet-core.md)  
