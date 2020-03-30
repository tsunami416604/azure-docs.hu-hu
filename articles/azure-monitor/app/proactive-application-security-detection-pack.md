---
title: Biztonsági észlelési csomag az Azure Application Insights szolgáltatással
description: Az Azure Application Insights és az Intelligens észlelés segítségével figyelheti az alkalmazást a lehetséges biztonsági problémákmiatt.
ms.topic: conceptual
ms.date: 12/12/2017
ms.openlocfilehash: b23ada6fe4596a2eee242cc9145789521caf697f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669726"
---
# <a name="application-security-detection-pack-preview"></a>Alkalmazásbiztonsági észlelési csomag (előzetes verzió)

Az Application Insights automatikusan elemzi az alkalmazás által létrehozott telemetriai adatokat, és észleli a lehetséges biztonsági problémákat. Ez a funkció lehetővé teszi a lehetséges biztonsági problémák azonosítását és kezelését az alkalmazás javításával vagy a szükséges biztonsági intézkedések megtételével.

Ez a funkció nem igényel speciális telepítést, [kivéve az alkalmazás konfigurálását a telemetriai adatok küldésére.](https://docs.microsoft.com/azure/application-insights/app-insights-usage-overview)

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Mikor kapom meg az ilyen típusú intelligens észlelési értesítést?
A rendszer háromféle biztonsági problémát észlel:
1. Nem biztonságos URL-hozzáférés: az alkalmazás URL-címe HTTP-n és HTTPS-en keresztül is elérhető. A HTTPS-kéréseket fogadó URL-címek általában nem fogadhatnak HTTP-kéréseket. Ez hibát vagy biztonsági problémát jelezhet az alkalmazásban.
2. Nem biztonságos űrlap: az alkalmazás ban lévő űrlap (vagy más "POST" kérés) HTTPS helyett HTTP-t használ. A HTTP használata veszélyeztetheti az űrlap által küldött felhasználói adatokat.
3. Gyanús felhasználói tevékenység: az alkalmazás elérése több országból/régióból érhető el, ugyanaz a felhasználó körülbelül ugyanabban az időben. Például ugyanaz a felhasználó ugyanabban az órában fért hozzá az alkalmazáshoz Spanyolországból és az Egyesült Államokból. Ez az észlelés az alkalmazás potenciálisan rosszindulatú hozzáférési kísérletét jelzi.

## <a name="does-my-app-definitely-have-a-security-issue"></a>Az alkalmazásomnak biztosan van biztonsági problémája?
Nem, az értesítés nem jelenti azt, hogy az alkalmazásnak biztosan van biztonsági problémája. A fenti forgatókönyvek észlelése sok esetben biztonsági problémát jelezhet. Az észlelés azonban természetes üzleti indoklással rendelkezhet, és figyelmen kívül hagyható.

## <a name="how-do-i-fix-the-insecure-url-access-detection"></a>Hogyan javíthatom a "Nem biztonságos URL-hozzáférés" észlelést?
1. **Osztályozás.** Az értesítés a nem biztonságos URL-ekhez hozzáférő felhasználók számát és a nem biztonságos hozzáférés által leginkább érintett URL-címet tartalmazza. Ez segíthet abban, hogy prioritást rendeljen a problémához.
2. **Hatókör.** A felhasználók hány százaléka fért hozzá a nem biztonságos URL-ekhez? Hány URL-t érintett? Ez az információ az értesítésből szerezhető be.
3. **Diagnosztizálni.** Az észlelés a nem biztonságos kérelmek listáját, valamint az érintett URL-címek és felhasználók listáját tartalmazza a probléma további diagnosztizálása érdekében.

## <a name="how-do-i-fix-the-insecure-form-detection"></a>Hogyan javíthatom a "Nem biztonságos űrlap" észlelést?
1. **Osztályozás.** Az értesítés a nem biztonságos űrlapok számát és azoknak a felhasználóknak a számát tartalmazza, akiknek az adatai potenciálisan veszélybe kerültek. Ez segíthet abban, hogy prioritást rendeljen a problémához.
2. **Hatókör.** Melyik forma vett részt a legtöbb bizonytalan átvitelben, és mi a nem biztonságos átvitelek idővel történő elosztása? Ez az információ az értesítésből szerezhető be.
3. **Diagnosztizálni.** Az észlelés a nem biztonságos űrlapok listáját és az egyes űrlapok nem biztonságos átviteleinek számát tartalmazza, hogy segítsen a probléma további diagnosztizálásában.

## <a name="how-do-i-fix-the-suspicious-user-activity-detection"></a>Hogyan javíthatom a "Gyanús felhasználói tevékenység" észlelést?
1. **Osztályozás.** Az értesítés a gyanús viselkedést mutató különböző felhasználók számát tartalmazza. Ez segíthet abban, hogy prioritást rendeljen a problémához.
2. **Hatókör.** Mely országokból/régiókból származtak a gyanús kérések? Melyik felhasználó volt a leggyanúsabb? Ez az információ az értesítésből szerezhető be.
3. **Diagnosztizálni.** Az észlelés a gyanús felhasználók listáját és az egyes felhasználók országainak/régióinak listáját tartalmazza, hogy segítsen a probléma további diagnosztizálásában.
