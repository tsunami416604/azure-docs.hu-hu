---
title: Az Azure App szolgáltatás kezelési |} A Microsoft Docs
description: Hogyan alkalmazások konfigurálása az Azure segítségével kapcsolhatja be és ki az alkalmazás igény szerinti szolgáltatások áttekintése
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
ms.openlocfilehash: 304ca08275ccf4bf32d38e3fc89dd5cf07460fa4
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413769"
---
# <a name="feature-management"></a>Funkciókezelés

Egy teljes újbóli üzembe helyezés magának az alkalmazásnak egy új application szolgáltatás hagyományosan szállítási van szükség. Szolgáltatás tesztelése, akkor valószínűséggel kell több alkalommal, amikor láthatóvá válik, vagy látni szeretné a fogadására jogosultak az alkalmazás üzembe helyezéséhez. A szolgáltatás egy modern szoftverek fejlesztési eljárás, amely a kód telepítése funkciókat tartalmazó kiadások leválasztja, és lehetővé teszi, hogy a szolgáltatás rendelkezésre állása, igény szerinti gyorsmódosítások. Nevezett technika használ *jelző funkció* (más néven *szolgáltatás be-vagy kikapcsolása*, *szolgáltatáskapcsoló*, és így tovább) dinamikusan felügyelheti egy szolgáltatás-életciklusának. Segít a fejlesztőknek az alábbi problémák kezelésére:

* **Ág forráskódkezelést**: A szolgáltatás jelzők gyakran használják burkolása alkalmazás funkciókat fejlesztés alatt áll. Ilyen funkciók "rejtett" alapértelmezés szerint. Biztonságosan küldhet el, ha befejezetlen, és éles környezetben inaktív marad. Ez a megközelítés nevű *sötét telepítési*, fel lehet szabadítani valamennyi a kód minden fejlesztési szakaszban végén. Már nincs szüksége a bármely kódot fiókja karbantartására akár több ciklus miatt a szolgáltatás végrehajtani egynél több ideig tart.
* **Éles környezetben végzett tesztelés**: A szolgáltatás jelzők segítségével éles környezetben új funkció korai hozzáférést. Például korlátozhatja a hozzáférést a csapattagok és a belső bétatesztelőknek. Ezek a felhasználók a teljes hűségű éles élmény helyett egy szimulált vagy részleges felület tesztelési környezetben fog kapni.
* **Fokozatos bevezetéssel**: A szolgáltatás jelzők bevezetni új funkciókat a végfelhasználók számára növekményes használhatja. Egy kis csoportja a felhasználói körhöz először célozza, és százalékosan fokozatosan növelje után végrehajtása magabiztosabb révén az idő múlásával.
* **Azonnali kill kapcsoló**: Szolgáltatás jelzők egy rejlő hálóként ad ki az új funkciókat biztosítanak. Ezek is nem csak kapcsolja be a de is, kikapcsolt Alkalmazásfunkciók könnyen. Ha megkapta a gyorsan letilthatják az egy funkciót, ha szükséges, építse újra, és az alkalmazás újbóli üzembe helyezése nélkül.
* **Szelektív aktiválási**: A legtöbb funkciót jelzők csak addig, amíg a kapcsolódó funkciók sikeresen kiadott létezik, míg egyes hosszú ideig is utolsó. Oszthatja a felhasználóit és a egy meghatározott készletének szolgáltatások továbbítására az egyes csoportok szerint használhatja őket. Például előfordulhat, hogy rendelkezik olyan szolgáltatás, amely egy bizonyos webböngészőben működik. A szolgáltatás azt a jelzőt határozhatja meg, hogy csak a felhasználók az adott böngésző, tekintse meg, és a funkció használatához. Ez a megközelítés előnye, hogy egyszerűen bővítheti a támogatott böngésző listában később, hogy a kód módosítása nélkül.

## <a name="basic-concepts"></a>Alapfogalmak

Az alábbiakban a szolgáltatás felügyeleti kapcsolatos számos új feltételek elfogadását.

* **Jelző funkció**: A szolgáltatás azt a jelzőt egy változót egy bináris állapotával *a* vagy *ki* , és egy ahhoz tartozó kódot letiltása. Állapotában úgy dönt, hogy a kódblokkot fut-e vagy sem.
* **A szolgáltatás manager**: A szolgáltatás manager olyan alkalmazáscsomagot jelent, amely az összes funkció jelzők az alkalmazások életciklusának kezeli. Általában további funkciókat, például a gyorsítótárazás jelzők és azok állapota frissítése biztosít.
* **Szűrő**: Szűrő egy funkció jelölője állapotának értékeléséhez szabály. Felhasználói csoport, az eszköz vagy a böngésző típusát, a földrajzi hely, időablak példák az összes szűrő jelenthetik.

A szolgáltatás felügyeleti hatékony megvalósítása vírusvédelmet használatához legalább két összetevőből áll: egy alkalmazás, amellyel jelzők funkció használatát és a egy külön tárházat, amely tárolja a szolgáltatás jelzők és azok aktuális állapota. A kapcsolati ábrázolása lent található.

## <a name="feature-flag-usage-in-code"></a>Funkciók jelző használata a kódban

Az egyszerű szolgáltatás jelzők megvalósítása az alkalmazás egyik egyszerű. A használt logikai állapot változóként conceptualize egy `if` feltételes utasítás a kódban.

```csharp
if (featureFlag) {
    // Run the following code
}
```

Ebben az esetben ha `featureFlag` értékre van állítva `True`, hajtja végre a mellékelt kódblokk; máskülönben kihagyja annak végrehajtását. Az érték `featureFlag` statikusan beállítható:

```csharp
bool featureFlag = true;
```

Azt is kiértékelése bizonyos szabályok alapján:

```csharp
bool featureFlag = isBetaUser();
```

Egy némileg összetettebb funkció jelző mintát is tartalmaz egy `else` utasítást is.

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

Ez is kell írni az alapszintű mintájában azonban. [A szolgáltatás jelzők használata az ASP.NET Core-alkalmazás](./use-feature-flags-dotnet-core.md) az előnye, hogy egy egyszerű kódmintát a szabványosításával jeleníti meg.

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
}

if (!featureFlag) {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>A szolgáltatás jelző tárház

Azok hatékony használatát, az alkalmazások használni minden funkció jelző externalize kell. Amelyek engedélyezik jelző Szolgáltatásállapotok módosíthatja a módosításával, és maga az alkalmazás újbóli telepítése nélkül. Az Azure App konfigurálása funkció jelzők központi adattára tervezték. Azt határozza meg a szolgáltatás jelzők különböző típusú, és azok állapota módosítására, gyors és magabiztos kiépítésében használhat. Elérheti a szolgáltatás jelző egyszerűen az alkalmazásból, az alkalmazás konfigurációs kódtárak használatával különböző programozási nyelv keretrendszerek. [A szolgáltatás jelzők használata az ASP.NET Core-alkalmazás](./use-feature-flags-dotnet-core.md) jeleníti meg, a .NET Core-alkalmazás konfigurációs szolgáltató és a szolgáltatás kezelési kódtárak használata együtt, a teljes megoldás megvalósításához az ASP.NET-alkalmazás funkció jelzők.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A szolgáltatás jelzők hozzáadása az ASP.NET Core-webalkalmazás](./quickstart-feature-flag-aspnet-core.md)  
