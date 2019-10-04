---
title: Ügyfélalkalmazások inicializálása (Microsoft Authentication Library JavaScripthez) | Azure
description: Ismerkedjen meg az ügyfélalkalmazások inicializálásával a JavaScripthez készült Microsoft Authentication Library (MSAL. js) használatával.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d36def4faa98f4b8e42c93cf3e222c2ec7ca89a
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073849"
---
# <a name="initialize-client-applications-using-msaljs"></a>Ügyfélalkalmazások inicializálása a MSAL. js használatával
Ez a cikk a Microsoft hitelesítési függvénytárának a JavaScripthez (MSAL. js) való inicializálását ismerteti egy felhasználói ügynök alkalmazás egy példányával. A User-Agent alkalmazás a nyilvános ügyfélalkalmazás olyan formája, amelyben az ügyfél kódja egy felhasználói ügynökön, például egy böngészőben fut. Ezek az ügyfelek nem tárolják a titkos kulcsokat, mivel a böngésző környezete nyíltan elérhető. Az ügyfélalkalmazások típusairól és az alkalmazás konfigurációs lehetőségeiről az [Áttekintés](msal-client-applications.md)című témakörben olvashat bővebben.

## <a name="prerequisites"></a>Előfeltételek
Az alkalmazás inicializálása előtt először regisztrálnia kell [a Azure Portal](scenario-spa-app-registration.md) , hogy az alkalmazás integrálható legyen a Microsoft Identity platformmal. A regisztráció után a következő információkra lehet szüksége (amelyek a Azure Portalban találhatók):

- Az ügyfél-azonosító (az alkalmazás GUID azonosítóját jelölő sztring)
- Az identitás-szolgáltató URL-címe (a példány neve) és az alkalmazás bejelentkezési célközönsége. Ez a két paraméter együttesen a hatóság néven ismert.
- A bérlő azonosítója, ha csak az Ön szervezete számára (egy egybérlős alkalmazás is megnevezett) üzletági alkalmazást ír.
- A webalkalmazások esetében meg kell adnia azt a redirectUri is, amelyben az identitás-szolgáltató visszatér az alkalmazáshoz a biztonsági jogkivonatokkal.

## <a name="initializing-applications"></a>Alkalmazások inicializálása

A MSAL. js a következőképpen használható egy egyszerű JavaScript/írógéppel-alkalmazásban. Inicializálja a MSAL hitelesítési környezetét egy `UserAgentApplication` konfigurációs objektummal való létrehozással. A MSAL. js inicializálásához szükséges minimális konfiguráció az alkalmazás clientID, amelyet az alkalmazás regisztrációs portálján kell megszereznie.

Az átirányítási folyamatokkal (`loginRedirect` és `acquireTokenRedirect`) rendelkező hitelesítési módszerekhez explicit módon regisztrálnia kell egy visszahívást a metóduson `handleRedirectCallback()` keresztüli sikeres vagy hiba esetén. Erre azért van szükség, mert az átirányítási folyamatok nem adnak vissza ígéreteket az előugró felülettel rendelkező metódusként.

```javascript
// Configuration object constructed
const config = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”
    }
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(config);

function authCallback(error, response) {
    //handle redirect response
}

// (optional when using redirect methods) register redirect call back for Success or Error
myMSALObj.handleRedirectCallback(authCallback);
```

A `UserAgentApplication` MSAL. js úgy lett kialakítva, hogy egyetlen példánnyal és konfigurációval rendelkezzen egyetlen hitelesítési környezettel. Több példány nem ajánlott, mert ütköző gyorsítótár-bejegyzéseket és viselkedést okoznak a böngészőben.

## <a name="configuration-options"></a>Beállítási lehetőségek

A MSAL. js egy olyan konfigurációs objektummal rendelkezik, amely a példányának `UserAgentApplication`létrehozásához rendelkezésre álló konfigurálható beállítások csoportosítását teszi lehetővé.

```javascript
type storage = "localStorage" | "sessionStorage";

// Protocol Support
export type AuthOptions = {
    clientId: string;
    authority?: string;
    validateAuthority?: boolean;
    redirectUri?: string | (() => string);
    postLogoutRedirectUri?: string | (() => string);
    navigateToLoginRequestUrl?: boolean;
};

// Cache Support
export type CacheOptions = {
    cacheLocation?: CacheLocation;
    storeAuthStateInCookie?: boolean;
};

// Library support
export type SystemOptions = {
    logger?: Logger;
    loadFrameTimeout?: number;
    tokenRenewalOffsetSeconds?: number;
};

// Developer App Environment Support
export type FrameworkOptions = {
    isAngular?: boolean;
    unprotectedResources?: Array<string>;
    protectedResourceMap?: Map<string, Array<string>>;
};

// Configuration Object
export type Configuration = {
    auth: AuthOptions,
    cache?: CacheOptions,
    system?: SystemOptions,
    framework?: FrameworkOptions
};
```

Alább látható a konfigurációs objektumban jelenleg támogatott konfigurálható beállítások teljes készlete:

- **clientID**: Kötelező. Az alkalmazás clientID az alkalmazás regisztrációs portálján kell megszereznie.

- **szolgáltató**: Nem kötelező. Egy olyan könyvtárat jelző URL-cím, amelyből a MSAL jogkivonatokat kérhet. Az alapértelmezett érték: `https://login.microsoftonline.com/common`.
    * &lt;Az Azure ad-ben az https://-példány&gt;/&lt;célközönsége&gt;, ahol &lt;a példány&gt; az identitás-szolgáltató tartománya (például `https://login.microsoftonline.com`) és acélközönség&gt; a bejelentkezési közönséget jelölő azonosító. &lt; Ez a következő értékeket veheti fel:
        * `https://login.microsoftonline.com/<tenant>`– a bérlő a bérlőhöz társított tartomány, például a contoso.onmicrosoft.com, vagy a címtár azon `TenantID` tulajdonságát jelképező GUID, amely kizárólag egy adott szervezet felhasználói számára való bejelentkezéshez használatos.
        * `https://login.microsoftonline.com/common`-Munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal való bejelentkezéshez használatos.
        * `https://login.microsoftonline.com/organizations/`-A felhasználók munkahelyi és iskolai fiókkal való bejelentkezéséhez használatos.
        * `https://login.microsoftonline.com/consumers/`– Csak a személyes Microsoft-fiók (élő) felhasználókkal való bejelentkezéshez használatos.
    * Azure ad B2C a (z) formában `https://<instance>/tfp/<tenant>/<policyName>/`van, ahol a példány a Azure ad B2C tartomány, a bérlő a Azure ad B2C bérlő neve, a policyName pedig az alkalmazandó B2C-szabályzat neve.


- **validateAuthority**: Nem kötelező.  A jogkivonatok kiállítójának ellenőrzése. Az alapértelmezett szint a `true`. A B2C-alkalmazások esetében, mivel a hatóság értéke ismert, és a szabályzatok eltérőek lehetnek, a hatóság ellenőrzése nem fog működni, és be `false`kell állítani a következőt:.

- **redirectUri**: Nem kötelező.  Az alkalmazás átirányítási URI-ja, ahol az alkalmazás elküldhet és fogadhat hitelesítési válaszokat. Pontosan meg kell egyeznie a portálon regisztrált átirányítási URI-k egyikével. Alapértelmezés szerint a `window.location.href`.

- **postLogoutRedirectUri**: Nem kötelező.  A kijelentkezés `postLogoutRedirectUri` után átirányítja a felhasználót. A mező alapértelmezett értéke: `redirectUri`.

- **navigateToLoginRequestUrl**: Nem kötelező. Kikapcsolhatja az alapértelmezett navigációt a Start lapon a bejelentkezés után. Az alapértelmezett érték a True (igaz). Ez csak az átirányítási folyamatok esetében használatos.

- **cacheLocation**: Nem kötelező.  A böngésző tárterületének beállítása `localStorage` a `sessionStorage`vagy a értékre. A mező alapértelmezett értéke: `sessionStorage`.

- **storeAuthStateInCookie**: Nem kötelező.  Ez a jelző a MSAL. js v 0.2.2-ben jelent meg, a Microsoft Internet Explorer és a Microsoft Edge [hitelesítési ciklusával kapcsolatos problémák](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones) elhárítása érdekében. A javítás kihasználása érdekében engedélyezze a jelzőt `storeAuthStateInCookie` True (igaz) értékre. Ha ez a beállítás engedélyezve van, a MSAL. js tárolja a hitelesítő kérések állapotát a böngésző cookie-jai hitelesítési folyamatainak érvényesítéséhez. Alapértelmezés szerint ez a jelző a következőre van beállítva `false`:.

- **logger**: Nem kötelező.  Egy visszahívási példánnyal rendelkező naplózó objektum, amelyet a fejlesztő biztosíthat, és egyéni módon tehet közzé naplókat. A Logger objektum átadásával kapcsolatos részletekért tekintse meg a [msal. js naplózása](msal-logging.md)című témakört.

- **loadFrameTimeout**: Nem kötelező.  Az Azure AD-beli jogkivonat-megújítási válasz előtt ennyi ezredmásodperc inaktivitást kell figyelembe venni. Az alapértelmezett érték 6 másodperc.

- **tokenRenewalOffsetSeconds**: Nem kötelező. Azon ezredmásodpercek száma, amelyek a token lejárata előtti megújításához szükséges eltolási időszakot határozzák meg. Az alapértelmezett érték 300 ezredmásodperc.

Ezek csak a MSAL szögletes burkoló könyvtárából érvényesek.
- **unprotectedResources**: Nem kötelező.  Nem védett erőforrásokhoz tartozó URI-k tömbje. A MSAL nem csatol jogkivonatot az ezen URI azonosítóval rendelkező kimenő kérelmekhez. Alapértelmezés szerint a `null`.

- **protectedResourceMap**: Nem kötelező.  Ez a MSAL által a hozzáférési jogkivonatok webes API-hívásokban való automatikus csatolásához használt hatókörökre való leképezése. A rendszer egyetlen hozzáférési jogkivonatot kapott az erőforráshoz. Így a következőképpen képezhető le egy adott erőforrás elérési útja: { https://graph.microsoft.com/v1.0/me "", ["user. Read"]} vagy az erőforrás alkalmazás URL-címe a következőként: https://graph.microsoft.com/ {"", ["user. Read", "mail. Send"]}. Ez a CORS-hívásokhoz szükséges. Alapértelmezés szerint a `null`.
