---
title: Problémák az Internet Explorerben & a Microsoft Edge-ben (MSAL. js) | Azure
titleSuffix: Microsoft identity platform
description: Az Internet Explorer és a Microsoft Edge böngészők használatával megismerheti a JavaScripthez készült Microsoft-hitelesítési függvénytár (MSAL. js) használata során felmerülő problémákat.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 5ae2dee68ec0da8e8a00d4f01583461462bc196c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76696095"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Az Internet Explorer és a Microsoft Edge böngészők ismert problémái (MSAL. js)

## <a name="issues-due-to-security-zones"></a>Biztonsági zónák miatti problémák
Az IE-ben és a Microsoft Edge-ben történt hitelesítéssel kapcsolatos hibákról több jelentést is készítettünk (mivel a *Microsoft Edge böngésző verziójának*frissítése a 40.15063.0.0-re). Ezeket követjük nyomon, és tájékoztatták a Microsoft Edge-csapatot. Míg a Microsoft Edge egy megoldáson dolgozik, itt látható a gyakran előforduló problémák és a megvalósítható lehetséges megkerülő megoldások leírása.

### <a name="cause"></a>Ok
A legtöbb probléma oka a következő: A munkamenet-tároló és a helyi tároló a Microsoft Edge böngésző biztonsági zónái között van particionálva. A Microsoft Edge ezen verziójában, amikor az alkalmazást átirányítják a zónák között, a munkamenet-tároló és a helyi tároló törlődik. A munkamenet-tárolót a böngésző normál navigációs sávjában törli, és a munkamenet és a helyi tárterület is törlődik a böngésző InPrivate módjában. A MSAL. js bizonyos állapotot ment a munkamenet-tárolóban, és ennek az állapotnak a ellenőrzésére támaszkodik a hitelesítési folyamatok során. Ha a munkamenet-tároló törlődik, ez az állapot elvész, ezért a rendszer hibás élményt eredményez.

### <a name="issues"></a>Hibák

- **A hitelesítés során a végtelen átirányítási hurkok és a lap újratöltődik**. Amikor a felhasználók bejelentkeznek az alkalmazásba a Microsoft Edge-ben, a rendszer visszairányítja őket a HRE bejelentkezési oldaláról, és egy végtelen átirányítási hurokba ragadnak, ami ismétlődő oldal újratöltését eredményezi. Ezt általában a munkamenet-tárolóban `invalid_state` lévő hiba kíséri.

- **Végtelen beszerzési jogkivonat-hurkok és AADSTS50058-hiba**. Ha egy Microsoft Edge-ben futó alkalmazás egy erőforráshoz tartozó jogkivonatot próbál beszerezni, az alkalmazás a beszerzési jogkivonat-hívás végtelen ciklusában is elakadhat, és a következő hibaüzenetet kaphatja a hálózati nyomkövetés HRE:

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- Az **előugró ablak nem zárul be, vagy a bejelentkező használatával történő bejelentkezéskor a hitelesítéshez beragadt**. Ha a Microsoft Edge vagy IE (InPrivate) előugró ablakán keresztül végez hitelesítést, a hitelesítő adatok beírása és a bejelentkezés után, ha több tartomány található a biztonsági zónákban, akkor az előugró ablak nem zárul le, mert a MSAL. js elveszti a leírót az előugró ablakban.  

### <a name="update-fix-available-in-msaljs-023"></a>Frissítés: elérhető javítás a MSAL. js 0.2.3
A hitelesítés átirányítási ciklusával kapcsolatos hibák javításait a [MSAL. js 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)tették közzé. A javítás kihasználása érdekében engedélyezze a jelölőt `storeAuthStateInCookie` a MSAL. js konfigurációban. Alapértelmezés szerint ez a jelző hamis értékre van állítva.

Ha a `storeAuthStateInCookie` jelző engedélyezve van, a MSAL. js a böngésző cookie-jait fogja használni a hitelesítési folyamatok érvényesítéséhez szükséges kérelem állapotának tárolásához.

> [!NOTE]
> Ez a javítás még nem érhető el a msal és msal-angularjs burkolók esetében. Ez a javítás nem foglalkozik a felugró ablakokkal kapcsolatos problémával.

Használja az alábbi áthidaló megoldásokat.

#### <a name="other-workarounds"></a>Egyéb megkerülő megoldások
Győződjön meg arról, hogy a probléma csak a Microsoft Edge böngésző adott verziójánál fordul elő, és a megkerülő megoldások elfogadása előtt működik a többi böngészőben.  
1. A problémák megkerülésének első lépéseként győződjön meg arról, hogy az alkalmazás tartománya, valamint a hitelesítési folyamat átirányításában részt vevő egyéb helyek megbízható helyként lesznek hozzáadva a böngésző biztonsági beállításaiban, hogy azok ugyanahhoz a biztonsági zónához tartozzanak.
Ehhez kövesse az alábbi lépéseket:
    - Nyissa meg az **Internet Explorert** , és kattintson a **Beállítások** (fogaskerék ikon) elemre a jobb felső sarokban
    - **Internetes beállítások** kiválasztása
    - Válassza a **Biztonság** fület.
    - A **megbízható helyek** lehetőségnél kattintson a **helyek** gombra, és adja hozzá az URL-címeket a megnyíló párbeszédpanelen.

2. Ahogy korábban említettük, mivel a normál Navigálás során csak a munkamenet-tárolót törli a rendszer, a MSAL. js-t úgy is beállíthatja, hogy a helyi tárterületet használja. Ez beállítható a MSAL inicializálásakor a `cacheLocation` konfigurációs paraméterként.

Vegye figyelembe, hogy ez nem oldja meg az InPrivate-böngészés hibáját, mivel a munkamenet és a helyi tárterület is törlődik.

## <a name="issues-due-to-popup-blockers"></a>Felugró ablakos blokkolók miatti problémák

Előfordulhat, hogy az előugró ablakok le vannak tiltva az IE-ben vagy a Microsoft Edge-ben, például ha egy második előugró ablak jelenik meg a multi-Factor Authentication során. A böngészőben megjelenik egy riasztás, amely lehetővé teszi, hogy az előugró ablak egyszer vagy bármikor elérhető legyen. Ha úgy dönt, hogy engedélyezi, a böngésző automatikusan megnyitja az előugró ablakot, `null` és visszaadja a hozzá tartozó leírót. Ennek eredményeképpen a függvénytár nem rendelkezik leíróval az ablakhoz, és nincs lehetőség az előugró ablak bezárására. Ugyanez a probléma nem fordul elő a Chrome-ban, amikor rákérdez, hogy engedélyezi-e a felugró ablakokat, mert nem nyit meg automatikusan előugró ablakot.

**Megkerülő megoldásként**a fejlesztőknek engedélyeznie kell a felugró ablakokat az IE-ben és a Microsoft Edge-ben, mielőtt elkezdik használni az alkalmazást a probléma elkerüléséhez.

## <a name="next-steps"></a>További lépések
További információ a [MSAL. js használatáról az Internet Explorerben](msal-js-use-ie-browser.md).
