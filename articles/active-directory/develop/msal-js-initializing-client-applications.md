---
title: Ügyfélalkalmazások (Microsoft-hitelesítési tár JavaScript-) inicializálása |} Az Azure
description: Ismerje meg a Microsoft-hitelesítési tár használatával (MSAL.js) JavaScript-ügyfélalkalmazások inicializálása.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd26f36356affbc8c272bd093757a8482773baf2
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544031"
---
# <a name="initialize-client-applications-using-msaljs"></a>Az ügyfélalkalmazások MSAL.js használatával inicializálása
Ez a cikk ismerteti inicializálása során Microsoft-hitelesítési tár JavaScript (MSAL.js) az olyan felhasználói ügynököt alkalmazás egy példányát. A felhasználói ügynök alkalmazás, amelyben az Ügyfélkód hajtja végre a felhasználó például egy webes böngésző ügynök egy nyilvános ügyfélalkalmazás egy formája. Ezek az ügyfelek ne tárolja a titkos adatait, mert némelyik érhető el a böngésző környezetet. Az ügyfél alkalmazástípusok és alkalmazáskonfigurációs beállítások kapcsolatos további információkért olvassa el a [áttekintése](msal-client-applications.md).

## <a name="prerequisites"></a>Előfeltételek
Alkalmazás inicializálása, előtt először létre kell [regisztrálja az Azure Portalon](scenario-spa-app-registration.md) úgy, hogy az alkalmazás integrálható a Microsoft identitásplatformjához. A regisztrációt követően szükség lehet a következő információkat (amely az Azure Portalon található):

- Az ügyfél-azonosító (egy GUID Azonosítót az alkalmazás képviselő karakterláncot)
- Az identitásszolgáltató szolgáltató URL-címe (a példány neve) és az alkalmazás bejelentkezési célközönség. E két paraméter együttesen hatóságként ismert.
- A bérlő Azonosítóját, ha a sor üzleti alkalmazás kizárólag a szervezetben (egybérlős alkalmazás elnevezett is ismert).
- A web apps esetében lesz, amelyben az identitásszolgáltató vissza fogja küldeni a biztonsági jogkivonatokat az alkalmazásokba is beállíthat a redirectUri.

## <a name="initializing-applications"></a>Alkalmazás inicializálása

MSAL.js módon használhatja egy egyszerű JavaScript-vagy Typescript-alkalmazásban. Inicializálja az MSAL hitelesítési környezetet úgy hárítható el `UserAgentApplication` egy konfigurációs objektumot. MSAL.js inicializálása a minimálisan szükséges konfigurációs clientID-t az alkalmazás, amely kell kap az alkalmazásregisztrációs portálon.

A hitelesítési módszerek az átirányítási forgalom (`loginRedirect` és `acquireTokenRedirect`), regisztráljon egy visszahívást siker vagy hiba keresztül kell `handleRedirectCallback()` metódust. Erre azért van szükség, mivel az átirányítási folyamatok nem adott vissza az ígéretek mint egy felugró kezelőfelülettel módszerek.

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

MSAL.js úgy tervezték, hogy rendelkezik egy egypéldányos és konfigurációja a `UserAgentApplication` , amelyek egy egyetlen hitelesítési környezetet. Több példány használata nem ajánlott, mivel azok kárt ütköző gyorsítótár-bejegyzéseket és a viselkedés a böngészőben.

## <a name="configuration-options"></a>Beállítási lehetőségek

MSAL.js konfigurációval rendelkezik, amely alább látható objektum egy példányát, az elérhető konfigurálható lehetőségekkel, csoportosítását biztosítja `UserAgentApplication`.

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

Alább a konfigurációs objektum által jelenleg támogatott konfigurálható beállítások teljes készletében van:

- **clientID**: Kötelező. Az alkalmazás clientID szerezheti be ezt a az alkalmazásregisztrációs portálon.

- **Szolgáltató**: Választható. Egy URL-cím egy könyvtárat, amely az MSAL származó jogkivonatokat kérhetnek jelzi. Alapértelmezett érték: `https://login.microsoftonline.com/common`.
    * Az Azure AD-ben van, az űrlap https://&lt;példány&gt;/&lt;célközönség&gt;, ahol &lt;példány&gt; az identity provider tartomány (például `https://login.microsoftonline.com`) és a &lt;célközönség&gt; bejelentkezési célközönség jelölő azonosítója. Ez lehet a következő értékeket:
        * `https://login.microsoftonline.com/<tenant>`-bérlő fogalmát, például contoso.onmicrosoft.com vagy a GUID jelölő a bérlőhöz hozzárendelt tartomány a `TenantID` annak a címtárnak, csak a használják a felhasználók egy adott szervezet tulajdonság.
        * `https://login.microsoftonline.com/common`– A felhasználók a munkahelyi és iskolai fiókokhoz vagy személyes Microsoft-fiókot használja.
        * `https://login.microsoftonline.com/organizations/`– Segítségével a felhasználók munkahelyi és iskolai fiókjába.
        * `https://login.microsoftonline.com/consumers/` – Segítségével a felhasználók csak személyes Microsoft-fiókkal (live).
    * A következő formában van az Azure AD B2C- `https://<instance>/tfp/<tenant>/<policyName>/`, ahol példány az Azure AD B2C-tartományban, bérlő az Azure AD B2C-bérlő nevét, a policyName a alkalmazni a B2C-szabályzat nevére.


- **validateAuthority**: Választható.  A tokenek kibocsátó érvényesítése. Az alapértelmezett szint a `true`. B2C-alkalmazásokhoz, mivel a szolgáltató érték ismert, és különböző, szabályzatonként lehet a szolgáltató érvényesítése nem fog működni és értékűre kell beállítani `false`.

- **redirectUri**: Választható.  Az átirányítási URI-ját az alkalmazás, ahol küldött és az alkalmazás által fogadott a hitelesítési válaszokat. Ez pontosan egyeznie kell az átirányítási URI-k a portál regisztrált egyik azzal a különbséggel, hogy az URL-kódolású kell lennie. Alapértelmezés szerint a `window.location.href`.

- **postLogoutRedirectUri**: Választható.  Átirányítja a felhasználót, hogy `postLogoutRedirectUri` Miután kijelentkezett. A mező alapértelmezett értéke: `redirectUri`.

- **navigateToLoginRequestUrl**: Választható. Kapcsolja ki az alapértelmezett navigációs kezdőlap bejelentkezés után lehetősége. Alapértelmezett érték az igaz. Ez csak az átirányítási folyamatok szolgál.

- **cacheLocation**: Választható.  Beállítja a böngésző storage egyaránt `localStorage` vagy `sessionStorage`. A mező alapértelmezett értéke: `sessionStorage`.

- **storeAuthStateInCookie**: Választható.  Ez a jelző MSAL.js v0.2.2 jelent, javítja a [hitelesítési hurok problémák](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones) a Microsoft Internet Explorer és Microsoft Edge. Engedélyezze a jelző `storeAuthStateInCookie` megoldásához igénybe vehetik ezt az IGAZ értéket. Ha ez engedélyezve van, MSAL.js tárolja a hitelesítési kérelem állapotának a hitelesítési folyamatok, a böngésző cookie-érvényesítés szükséges. Alapértelmezés szerint ez a jelző van beállítva, `false`.

- **logger**: Választható.  A naplózó objektum egy visszahívási példányt, amely a fejlesztő fogadni, és a naplók közzététele egy egyéni módon adható meg. Naplózó objektum átadását a részletekért lásd: [msal.js naplózás](msal-logging.md).

- **loadFrameTimeout**: Választható.  Ezredmásodpercben kell alkalmazni az Azure AD-ből token megújításának választ, inaktivitás időkorlátja lejárt. Alapértelmezett érték 6 másodperc.

- **tokenRenewalOffsetSeconds**: Választható. Amely az ablak eltolási a lejárat előtt jogkivonat megújításához szükséges idő ezredmásodpercben száma. Az alapértelmezett érték 300 ezredmásodperc.

A következők csak átadandó az MSAL Angular burkoló erőforrástárból alkalmazható:
- **unprotectedResources**: Választható.  URI-k, amelyek nem védett erőforrásokra tömbje. Az MSAL nem csatolása egy token kimenő kérelmek, amelyek ezen URI-t. Alapértelmezés szerint a `null`.

- **protectedResourceMap**: Választható.  Ez a hatókörök automatikusan csatolja a webes API-hívások hozzáférési jogkivonatok az MSAL által használt erőforrások feltérképezése. Az erőforrás kapjuk meg egyetlen hozzáférési tokennel. Így a következőképpen leképezhet egy adott erőforrás elérési útja: {"https://graph.microsoft.com/v1.0/me", ["user.read"]}, vagy az alkalmazás URL-címét, az erőforrás: {"https://graph.microsoft.com/", ["user.read", "mail.send"]}. Ez a CORS-hívásokhoz szükséges. Alapértelmezés szerint a `null`.
