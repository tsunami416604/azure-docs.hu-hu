---
title: Ismert problémák a böngészőkben (Microsoft-hitelesítési tár JavaScript-) |} Az Azure
description: Ismert problémák a Microsoft-hitelesítési tár JavaScript (MSAL.js) használatakor a ismerteti az Internet Explorer és Microsoft Edge böngészőben.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c57ed956ec50c8bac26720a27894c07353928336
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873900"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-with-msaljs"></a>Az Internet Explorer és Microsoft Edge böngésző MSAL.js az ismert problémák

## <a name="issues-due-to-security-zones"></a>Problémák miatt a biztonsági zónák
Több jelentést is az Internet Explorer és Microsoft Edge-hitelesítéssel kapcsolatos hibák kellett (frissítése óta a *Microsoft Edge böngésző verziója, 40.15063.0.0*). Ezek nyomon követ, és amelyet a Microsoft Edge csapata értesíti. Microsoft Edge-megoldás működik, amíg itt van a gyakran előforduló hibákat és a lehetséges megoldások futtatható leírása.

### <a name="cause"></a>Ok
Ezeket a problémákat a legtöbb oka a következők szerint. A munkamenet-tároló és a helyi tároló biztonsági zónák a Microsoft Edge böngésző szerint vannak particionálva. A Microsoft Edge adott verziójában amikor a rendszer átirányítja az alkalmazás több zónában, a munkamenet-tároló és a helyi tároló törlődik. Pontosabban a munkamenet-tárolót a szokásos böngésző navigációs törlődik, és a munkamenet- és a helyi tároló törlődik, a böngésző InPrivate módban. MSAL.js adott állapotra menti a munkamenet-storage-ban, és ez az állapot ellenőrzése a hitelesítési folyamatok során támaszkodik. Ha a munkamenet-tároló üres, ebben az állapotban elvész, és ezért nem működő funkciókat eredményez.

### <a name="issues"></a>Problémák

- **Végtelen átirányítási ciklusok és az oldal újra betölti a hitelesítés során**. Amikor a felhasználók bejelentkeznek az alkalmazásba a Microsoft Edge, visszaküldi az aad-ben a bejelentkezési lapot a rendszer átirányítja, és regisztrálásuk végtelen átirányítási ciklusba ismétlődő lapon újra eredményez. Ez általában együtt egy `invalid_state` hiba történt a munkamenet-storage-ban.

- **Végtelen beszerezni a jogkivonatot hurkokat és AADSTS50058 hiba**. Amikor az alkalmazások futtatása a Microsoft Edge egy erőforráshoz egy token beszerzéséhez, az alkalmazás lehetséges, hogy elakadnak a beolvasási token hívás együtt a következő hiba történt az aad-ből a hálózati nyomkövetést a végtelen ciklust:

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- **Előugró ablak nem zárja be, vagy a bejelentkezési előugró keresztül hitelesítést használatakor elakadt**. Hitelesítésekor keresztül előugró ablakban, a Microsoft Edge vagy IE(InPrivate), bejelentkezés, ha a biztonsági zónák között több tartományt is érint, a navigációs és a hitelesítő adatok megadása után az előugró ablak nem zárható be, mert MSAL.js elveszíti a fogópont az előugró ablakban.  

    Az alábbiakban ezeket a problémákat a Microsoft Edge problémakövetőt mutató hivatkozásokat:  
    - [Programhiba 13861050](https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/13861050/)
    - [Programhiba 13861663](https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/13861663/)

### <a name="update-fix-available-in-msaljs-023"></a>Frissítés: Javítás érhető el az MSAL.js 0.2.3
Kijavítja a adtak ki a hitelesítési átirányítási hurok problémák [MSAL.js 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases). Engedélyezze a jelző `storeAuthStateInCookie` a MSAL.js config javítás előnyeinek kihasználása érdekében. Alapértelmezés szerint ez a jelző false értékre van beállítva.

Ha a `storeAuthStateInCookie` jelző engedélyezve van, akkor MSAL.js a böngésző cookie-kat használja, a szükséges hitelesítési folyamatok érvényesítési kérés állapot tárolásához.

> [!NOTE]
> Ez a javítás még nem használható az msal angular és az msal-angularjs burkolókat. Ez a javítás nem oldja meg a probléma az előugró windows.

Használja az alábbi megkerülő megoldások.

#### <a name="other-workarounds"></a>Megkerülő megoldások
Győződjön meg arról, hogy a probléma csak a Microsoft Edge böngésző és az egyéb böngészők adott verziójához történik ezek a megoldások bevezetése előtti tesztelése.  
1. Ezek a problémák elkerülése érdekében első lépésként ellenőrizze, hogy az alkalmazás tartományhoz, és a hitelesítési folyamat az átirányítások részt további helyek hozzá szeretné adni a böngésző biztonsági beállításainak lévő megbízható helyekhez, hogy az azonos biztonsági zónában tartoznak.
Ehhez kövesse az alábbi lépéseket:
    - Nyissa meg **az Internet Explorer** , majd kattintson a a **beállítások** (fogaskerék ikon) a jobb felső sarokban található
    - Válassza ki **Internetbeállítások**
    - Válassza ki a **biztonsági** lap
    - Alatt a **megbízható helyek** lehetőséget, majd kattintson a a **helyek** gombra, és a megnyíló párbeszédpanelen adja hozzá az URL-címeket.

2. Ahogy említettük, mielőtt csak a munkamenet óta tárolási nincs bejelölve, a rendszeres navigáció során, konfigurálhatja a MSAL.js inkább a helyi tároló használata. A következőképpen adhatja meg a `cacheLocation` konfigurációs paraméter MSAL inicializálása során.

Vegye figyelembe, hogy ez nem lehet megoldani a problémát az InPrivate-böngészés, mivel mind a munkamenetet, és a helyi tároló nincs bejelölve.

## <a name="issues-due-to-popup-blockers"></a>Előugró ablakok blokkolása miatt problémák

Előfordulhatnak olyan esetek, amikor az előugró ablakok le vannak tiltva, az Internet Explorer vagy a Microsoft Edge, például amikor egy második előugró ablak következik-e a multi-factor authentication szolgáltatás. Riasztást kap, hogy a felugró ablak egyszer vagy mindig a böngészőben. Ha engedélyezését választja, a böngésző automatikusan megnyitja az előugró ablakban, és adja vissza egy `null` kezelni azt. Ennek eredményeképpen a függvénytár nem rendelkezik leíró a az ablak, és nem lehet az előugró ablak bezárásához. Ugyanez a probléma nem történik a Chrome-ban, ha azt kéri, az előugró ablakok engedélyezése, mert ez nem automatikusan nyitja meg egy másik előugró ablak.

Mint egy **megoldás**, fejlesztők kell az Internet Explorer és Microsoft Edge előugró ablakok engedélyezése, hogy az alkalmazás használatával a probléma elkerülése érdekében indítása előtt.

## <a name="next-steps"></a>További lépések
Tudjon meg többet [MSAL.js használatával az Internet Explorer](msal-js-use-ie-browser.md).
