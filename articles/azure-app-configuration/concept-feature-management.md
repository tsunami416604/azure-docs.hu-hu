---
title: Azure-alkalmazás konfigurációs funkcióinak kezelése
description: Áttekintés arról, hogyan használható az Azure app Configuration az alkalmazások igény szerinti funkciójának bekapcsolásához és kikapcsolásához.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: 85228854f6c106c68cedc3eeea81e15fd662774a
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76898733"
---
# <a name="feature-management-overview"></a>A szolgáltatások kezelése – áttekintés

Az új alkalmazás-szolgáltatás szállítása hagyományosan az alkalmazás teljes újratelepítését igényli. Egy szolgáltatás teszteléséhez valószínűleg az alkalmazást többször kell központilag telepíteni ahhoz, hogy a funkció láthatóvá válik, vagy ki kapja meg.

A szolgáltatás-felügyelet egy modern szoftverfejlesztői gyakorlat, amely elválasztja a funkció kiadását a kód központi telepítéséről, és lehetővé teszi az igény szerinti szolgáltatás rendelkezésre állásának gyors módosítását. Ez a funkció *jelzői* (más néven *funkció-váltás*, *funkció-kapcsolók*stb.) elnevezésű technikát használja a szolgáltatások életciklusának dinamikus felügyeletéhez.

A szolgáltatás-felügyelet segítségével a fejlesztők a következő problémákkal foglalkoznak:

* **Kód elágazásának felügyelete**: a funkciók jelzőit gyakran használják a fejlesztés alatt álló új alkalmazás-funkciók becsomagolására. Az ilyen funkciók alapértelmezés szerint rejtettek. Biztonságosan szállíthatja a szolgáltatást, annak ellenére, hogy befejezetlen állapotban van, és az éles környezetben marad. Ha ezt a módszert használja, a *sötét üzembe helyezésnek*nevezett módszer minden egyes fejlesztési ciklus végén felszabadítja az összes kódot. Többé nem kell megtartania a kód elágazását több ciklusban, mert egy olyan szolgáltatás, amely egynél több ciklust hajt végre.
* **Tesztelés éles**környezetben: a funkciók jelzői segítségével korai hozzáférést biztosíthat az éles környezetben futó új funkciókhoz. Például korlátozhatja ezt a hozzáférést csak a csapattagok vagy a belső bétaverziós tesztelők számára. Ezek a felhasználók teljes körűen kihasználják az éles környezetet, és nem a tesztelési környezetben szimulált, sem részleges élményt nyújtanak.
* **Beléptetés: a**funkciók jelzői segítségével fokozatosan, a végfelhasználók számára új funkciókat érhet el. Először is megcélozhatja a felhasználói populáció kis hányadát, és az idő múlásával fokozatosan növelheti a százalékos arányt, miután nagyobb megbízhatóságot szerzett a megvalósításban.
* **Instant kill kapcsoló**: a funkciók jelzői egy belső biztonsági hálót biztosítanak az új funkciók felszabadításához. Velük azonnal be-és kikapcsolhatja az alkalmazás funkcióit. Ha szükséges, az alkalmazás újraépítése és újbóli üzembe helyezése nélkül is gyorsan letilthatja a szolgáltatást.
* **Szelektív aktiválás**: noha a legtöbb funkció-jelző csak a hozzájuk tartozó funkciók sikeres kiadása után van közzétéve, néhány hosszú ideig tarthat. A szolgáltatás-jelzők használatával szegmentálhatja a felhasználókat, és adott szolgáltatásokat biztosíthat az egyes csoportoknak. Előfordulhat például, hogy egy olyan szolgáltatással rendelkezik, amely csak bizonyos webböngészőn működik. Meghatározhatja a szolgáltatás jelölőjét, így csak a böngésző felhasználói láthatják és használhatják a szolgáltatást. Ezzel a módszerrel később egyszerűen kiterjesztheti a támogatott böngészőket a kód módosítása nélkül.

## <a name="basic-concepts"></a>Alapfogalmak

A szolgáltatások felügyeletével kapcsolatos további feltételek:

* **Szolgáltatás jelzője**: a szolgáltatás jelölője a *be* -vagy *kikapcsolt*bináris állapotú változó. A szolgáltatás jelzője egy kapcsolódó kódrészletet is tartalmaz. A szolgáltatás jelző állapota elindítja, hogy a kód blokkolja-e vagy sem.
* A Feature **Manager**: a Feature Manager egy alkalmazáscsomag, amely az alkalmazás összes szolgáltatás jelzőjének életciklusát kezeli. A Feature Manager általában további funkciókat biztosít, például gyorsítótárazási funkció jelzőit és az állapotuk frissítését.
* **Szűrő**: a szűrő egy olyan szabály, amely egy szolgáltatás jelzője állapotát értékeli. Egy felhasználói csoport, egy eszköz vagy egy böngésző típusa, egy földrajzi hely és egy időablak, amely a szűrők által ábrázolható példákat mutatja be.

A szolgáltatások felügyeletének hatékony megvalósítása legalább két, a koncerten dolgozó összetevőből áll:

* Egy alkalmazás, amely a funkciók jelzőit használja.
* Egy különálló adattár, amely a szolgáltatás jelölőit és aktuális állapotait tárolja.

Az alábbi példák szemléltetik, hogyan hatnak ezek az összetevők.

## <a name="feature-flag-usage-in-code"></a>Funkció-jelölő használata a kódban

A szolgáltatás-jelzők alkalmazásban való megvalósításának alapvető mintája egyszerű. A szolgáltatás jelölőjét logikai állapot változóként tekintheti meg, amelyet a kód `if` feltételes utasítása használ:

```csharp
if (featureFlag) {
    // Run the following code
}
```

Ebben az esetben, ha a `featureFlag` `True`re van beállítva, a rendszer végrehajtja a mellékelt kódrészletet. Ellenkező esetben a rendszer kihagyja. `featureFlag` statikusan állíthatja be a értéket, ahogy az a következő példában látható:

```csharp
bool featureFlag = true;
```

A jelző állapotát bizonyos szabályok alapján is kiértékelheti:

```csharp
bool featureFlag = isBetaUser();
```

Egy kicsit bonyolultabb funkció jelző mintázata `else` utasítást is tartalmaz:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

Ezt a viselkedést az alapszintű mintában is újraírhatja. A [ASP.net Core alkalmazásban található szolgáltatás-jelzők használatával megtekintheti](./use-feature-flags-dotnet-core.md) az egyszerű kód mintájának egységesítésének előnyeit. Példa:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
}

if (!featureFlag) {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>Szolgáltatások jelző tárháza

Ahhoz, hogy hatékonyan használhassa a szolgáltatás jelzőit, meg kell Externalize az alkalmazásban használt összes szolgáltatás jelölőjét. Ez a módszer lehetővé teszi, hogy az alkalmazás módosítása és újbóli üzembe helyezése nélkül módosítsa a szolgáltatás jelölőjét.

Az Azure-alkalmazások konfigurációjának célja, hogy központosított tárház legyen a funkciók jelzői számára. A használatával különböző típusú szolgáltatás-jelzőket adhat meg, és gyorsan és magabiztosan kezelheti az állapotukat. Ezután az alkalmazás konfigurációs kódtárai különböző programozási nyelvi keretrendszerekhez használhatók, így egyszerűen hozzáférhetnek ezekhez a szolgáltatásokhoz.

A [ASP.net Core alkalmazásban látható funkció-jelzők](./use-feature-flags-dotnet-core.md) azt mutatják be, hogyan használhatók a .net Core alkalmazás-konfiguráló szolgáltató és a szolgáltatás-felügyeleti kódtárak a ASP.net-webalkalmazáshoz tartozó szolgáltatási jelzők megvalósításához.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Szolgáltatás-jelzők hozzáadása egy ASP.NET Core webalkalmazáshoz](./quickstart-feature-flag-aspnet-core.md)  
