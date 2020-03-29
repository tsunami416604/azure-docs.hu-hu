---
title: Problémák az Internet Explorer & a Microsoft Edge (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogy miként merülnek fel a Microsoft JavaScript hitelesítési kódtárának (MSAL.js) Internet Explorer és Microsoft Edge böngészőkkel való használata korával kapcsolatos problémákról.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696095"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Ismert problémák az Internet Explorer és a Microsoft Edge böngészőkben (MSAL.js)

## <a name="issues-due-to-security-zones"></a>Biztonsági zónák miatti problémák
Az IE és a Microsoft Edge hitelesítésével kapcsolatos problémákról több jelentést is beszámoltunk (a Microsoft Edge böngésző verziójának frissítése óta *40.15063.0.0).* Nyomon követjük ezeket, és tájékoztattuk a Microsoft Edge csapatát. Bár a Microsoft Edge megoldáson dolgozik, itt van a gyakran előforduló problémák és a lehetséges lehetséges lehetséges megoldások leírása.

### <a name="cause"></a>Ok
Az oka a legtöbb ilyen kérdések a következők. A munkamenet-tárolót és a helyi tárolót a Microsoft Edge böngésző biztonsági zónái particionálják. A Microsoft Edge ezen verziójában, amikor az alkalmazás zónák között van átirányítva, a munkamenet-tároló és a helyi tároló törlődik. Pontosabban a munkamenet-tároló törlődik a normál böngészőnavigációban, és mind a munkamenet, mind a helyi tároló törlődik a böngésző InPrivate-módjában. Az MSAL.js bizonyos állapotot ment a munkamenet-tárolóban, és a hitelesítési folyamatok során ennek az állapotnak az ellenőrzésére támaszkodik. A munkamenet-tároló törlésekor ez az állapot elvész, és így megszakadt élményeket eredményez.

### <a name="issues"></a>Hibák

- **Végtelen átirányítási hurkok és oldal újratöltése a hitelesítés során**. Amikor a felhasználók bejelentkeznek az alkalmazásba a Microsoft Edge-en, a rendszer visszairányítja őket az AAD bejelentkezési oldalról, és végtelen átirányítási hurokba kerülnek, ami ismétlődő oldalújratöltést eredményez. Ezt általában a `invalid_state` munkamenet-tároló hibája kíséri.

- **Végtelen megszerezni token hurkok és AADSTS50058 hiba**. Amikor egy Microsoft Edge-en futó alkalmazás megpróbál jogkivonatot beszerezni egy erőforráshoz, az alkalmazás a beszerzési jogkivonat-hívás végtelen ciklusában ragadhat, valamint a hálózati nyomkövetésa során az AAD következő hibája:

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- **Az előugró ablak nem záródik be, vagy beragadt, amikor a bejelentkezést a Popup-on keresztül használja a hitelesítéshez.** A Microsoft Edge vagy az IE(InPrivate) előugró ablakán keresztül történő hitelesítéskor a hitelesítő adatok megadása és a bejelentkezés után, ha a navigációs zónában több tartomány is részt vesz, az előugró ablak nem zárul be, mert az MSAL.js elveszíti a az előugró ablakot.  

### <a name="update-fix-available-in-msaljs-023"></a>Update: Fix elérhető MSAL.js 0.2.3
A hitelesítési átirányítási ciklussal kapcsolatos problémák javításai az [MSAL.js 0.2.3-ban](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)jelentek meg. Engedélyezze `storeAuthStateInCookie` a jelzőt az MSAL.js config-ban, hogy kihasználja ezt a javítást. Alapértelmezés szerint ez a jelző hamis értékre van állítva.

Ha `storeAuthStateInCookie` a jelző engedélyezve van, az MSAL.js a böngésző cookie-kat fogja használni az auth-folyamatok érvényesítéséhez szükséges kérelemállapot tárolására.

> [!NOTE]
> Ez a javítás még nem áll rendelkezésre a msal-szög és msal-szög-szögcsomagolók. Ez a javítás nem oldja meg az előugró ablakokkal kapcsolatos problémát.

Az alábbi kerülő megoldásokat használhatja.

#### <a name="other-workarounds"></a>Egyéb megoldások
Győződjön meg arról, hogy a probléma csak a Microsoft Edge böngésző adott verzióján jelentkezik, és a többi böngészőben is működik, mielőtt ezeket a megoldásokat alkalmaznák.  
1. Első lépésként a problémák megkerüléséhez győződjön meg arról, hogy az alkalmazástartomány és a hitelesítési folyamat átirányításaiban részt vevő bármely más webhely megbízható helyként kerül hozzáadásra a böngésző biztonsági beállításaiközött, hogy azok ugyanahhoz a biztonsági zónához tartozzanak.
Ehhez kövesse az alábbi lépéseket:
    - Nyissa meg **az Internet Explorert,** és kattintson a **beállításokra** (fogaskerék ikonra) a jobb felső sarokban
    - **Internetbeállítások kiválasztása**
    - A Biztonság lap **kijelölése**
    - A **Megbízható helyek csoportban** kattintson a **Webhelyek** gombra, és adja hozzá az URL-címeket a megnyíló párbeszédpanelen.

2. Ahogy korábban említettük, mivel csak a munkamenet-tároló törlődik a normál navigáció során, beállíthatja az MSAL.js-t a helyi tároló használatára. Ez az MSAL `cacheLocation` inicializálása során konfigurációs paraméterként állítható be.

Megjegyzés: ez nem oldja meg az InPrivate-böngészés problémáját, mivel mind a munkamenet, mind a helyi tároló törlődik.

## <a name="issues-due-to-popup-blockers"></a>Az előugró ablakok blokkolása miatti problémák

Vannak olyan esetek, amikor az ie vagy a Microsoft Edge levan tiltva, például amikor egy második előugró ablak történik a többtényezős hitelesítés során. Kapsz egy figyelmeztetést a böngészőben, hogy a lakosság egyszer vagy mindig. Ha úgy dönt, hogy engedélyezi, a böngésző automatikusan megnyitja az előugró ablakot, és visszaad egy `null` leírót. Ennek eredményeképpen a könyvtár nem rendelkezik az ablak leírójával, és nincs mód az előugró ablak bezárására. Ugyanez a probléma nem fordul elő a Chrome-ban, amikor kéri, hogy engedélyezze a felugró ablakokat, mert nem nyit meg automatikusan egy előugró ablakot.

A **probléma elkerülése érdekében a**fejlesztőknek engedélyezniük kell az IE és a Microsoft Edge előugró ablakait, mielőtt elkezdenék használni az alkalmazásukat.

## <a name="next-steps"></a>További lépések
További információ [az MSAL.js használatáról az Internet Explorer programban.](msal-js-use-ie-browser.md)
