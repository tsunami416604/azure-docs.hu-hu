---
title: MSAL. js-ügyfélalkalmazások inicializálása | Azure
titleSuffix: Microsoft identity platform
description: Ismerkedjen meg az ügyfélalkalmazások inicializálásával a JavaScripthez készült Microsoft Authentication Library (MSAL. js) használatával.
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/12/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b041d8777f81f1796a2e2f7926f324e3b601bd93
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74916502"
---
# <a name="initialize-client-applications-using-msaljs"></a>Ügyfélalkalmazások inicializálása a MSAL. js használatával
Ez a cikk a Microsoft hitelesítési függvénytárának a JavaScripthez (MSAL. js) való inicializálását ismerteti egy felhasználói ügynök alkalmazás egy példányával. A User-Agent alkalmazás a nyilvános ügyfélalkalmazás olyan formája, amelyben az ügyfél kódja egy felhasználói ügynökön, például egy böngészőben fut. Ezek az ügyfelek nem tárolják a titkos kulcsokat, mivel a böngésző környezete nyíltan elérhető. Az ügyfélalkalmazások típusairól és az alkalmazás konfigurációs lehetőségeiről az [Áttekintés](msal-client-applications.md)című témakörben olvashat bővebben.

## <a name="prerequisites"></a>Előfeltételek
Az alkalmazás inicializálása előtt először [regisztrálnia kell a Azure Portal](scenario-spa-app-registration.md) , hogy az alkalmazás integrálható legyen a Microsoft Identity platformmal. A regisztráció után a következő információkra lehet szüksége (amelyek a Azure Portalban találhatók):

- Az ügyfél-azonosító (az alkalmazás GUID azonosítóját jelölő sztring)
- Az identitás-szolgáltató URL-címe (a példány neve) és az alkalmazás bejelentkezési célközönsége. Ez a két paraméter együttesen a hatóság néven ismert.
- A bérlő azonosítója, ha csak az Ön szervezete számára (egy egybérlős alkalmazás is megnevezett) üzletági alkalmazást ír.
- A webalkalmazások esetében meg kell adnia azt a redirectUri is, amelyben az identitás-szolgáltató visszatér az alkalmazáshoz a biztonsági jogkivonatokkal.

## <a name="initializing-applications"></a>Alkalmazások inicializálása

A MSAL. js a következőképpen használható egy egyszerű JavaScript/írógéppel-alkalmazásban. Inicializálja a MSAL hitelesítési környezetét `UserAgentApplication` konfigurációs objektummal való létrehozásával. A MSAL. js inicializálásához szükséges minimális konfiguráció az alkalmazás clientID, amelyet az alkalmazás regisztrációs portálján kell megszereznie.

Az átirányítási folyamatokkal (`loginRedirect` és `acquireTokenRedirect`ekkel) rendelkező hitelesítési módszerek esetében explicit módon regisztrálnia kell a sikeres vagy sikertelen visszahívást `handleRedirectCallback()` metódus használatával. Erre azért van szükség, mert az átirányítási folyamatok nem adnak vissza ígéreteket az előugró felülettel rendelkező metódusként.

```javascript
// Configuration object constructed
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
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

A MSAL. js úgy lett kialakítva, hogy a `UserAgentApplication` egyetlen példányát és konfigurációját használja egyetlen hitelesítési környezet ábrázolására. Több példány nem ajánlott, mert ütköző gyorsítótár-bejegyzéseket és viselkedést okoznak a böngészőben.

## <a name="configuration-options"></a>Beállítási lehetőségek

A MSAL. js egy olyan konfigurációs objektummal rendelkezik, amely a `UserAgentApplication`egy példányának létrehozásához rendelkezésre álló konfigurálható beállítások csoportosítását teszi lehetővé.

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
    navigateFrameWait?: number;
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

- **clientID**: kötelező. Az alkalmazás clientID az alkalmazás regisztrációs portálján kell megszereznie.

- **szolgáltató**: nem kötelező. Egy olyan könyvtárat jelző URL-cím, amelyből a MSAL jogkivonatokat kérhet. Az alapértelmezett érték: `https://login.microsoftonline.com/common`.
    * Az Azure AD-ben a https://&lt;példány&gt;/&lt;célközönség&gt;, ahol &lt;példány&gt; az identitás-szolgáltató tartománya (például `https://login.microsoftonline.com`) és &lt;célközönség&gt; a bejelentkezési célközönséget jelképező azonosító. Ez a következő értékeket veheti fel:
        * `https://login.microsoftonline.com/<tenant>`– a bérlő olyan tartományhoz van társítva, mint a contoso.onmicrosoft.com, vagy a címtár `TenantID` tulajdonságát jelölő GUID, amely csak egy adott szervezet felhasználóinak való bejelentkezéshez használatos.
        * `https://login.microsoftonline.com/common`– a felhasználók munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal való bejelentkezéshez használhatók.
        * `https://login.microsoftonline.com/organizations/`– a felhasználók munkahelyi és iskolai fiókkal való bejelentkezéshez használhatók.
        * `https://login.microsoftonline.com/consumers/` – csak a személyes Microsoft-fiók (élő) felhasználókkal való bejelentkezéshez használatos.
    * Azure AD B2C az űrlap `https://<instance>/tfp/<tenant>/<policyName>/`, ahol a példány a Azure AD B2C tartomány, a bérlő a Azure AD B2C bérlő neve, a policyName pedig az alkalmazandó B2C-szabályzat neve.


- **validateAuthority**: nem kötelező.  A jogkivonatok kiállítójának ellenőrzése. Az alapértelmezett érték a `true`. A B2C-alkalmazások esetében, mivel a hatóság értéke ismert, és a szabályzatok eltérőek lehetnek, a hatóság ellenőrzése nem fog működni, és a `false`re kell beállítani.

- **redirectUri**: nem kötelező.  Az alkalmazás átirányítási URI-ja, ahol az alkalmazás elküldhet és fogadhat hitelesítési válaszokat. Pontosan meg kell egyeznie a portálon regisztrált átirányítási URI-k egyikével. Az alapértelmezett érték a `window.location.href`.

- **postLogoutRedirectUri**: nem kötelező.  A kijelentkezés után `postLogoutRedirectUri` átirányítja a felhasználót. Az alapértelmezett érték `redirectUri`.

- **navigateToLoginRequestUrl**: nem kötelező. Kikapcsolhatja az alapértelmezett navigációt a Start lapon a bejelentkezés után. Alapértelmezett érték: true (igaz). Ez csak az átirányítási folyamatok esetében használatos.

- **cacheLocation**: nem kötelező.  A böngésző tárterületét `localStorage` vagy `sessionStorage`re állítja be. A mező alapértelmezett értéke: `sessionStorage`.

- **storeAuthStateInCookie**: nem kötelező.  Ez a jelző a MSAL. js v 0.2.2-ben jelent meg, a Microsoft Internet Explorer és a Microsoft Edge [hitelesítési ciklusával kapcsolatos problémák](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones) elhárítása érdekében. A javítás használatának kihasználása érdekében engedélyezze a jelző `storeAuthStateInCookie`t igaz értékre. Ha ez a beállítás engedélyezve van, a MSAL. js tárolja a hitelesítő kérések állapotát a böngésző cookie-jai hitelesítési folyamatainak érvényesítéséhez. Alapértelmezés szerint ez a jelző `false`ra van beállítva.

- **naplózó**: nem kötelező.  Egy visszahívási példánnyal rendelkező naplózó objektum, amelyet a fejlesztő biztosíthat, és egyéni módon tehet közzé naplókat. A Logger objektum átadásával kapcsolatos részletekért tekintse meg a [msal. js naplózása](msal-logging.md)című témakört.

- **loadFrameTimeout**: nem kötelező.  Az Azure AD-beli jogkivonat-megújítási válasz előtt ennyi ezredmásodperc inaktivitást kell figyelembe venni. Az alapértelmezett érték 6 másodperc.

- **tokenRenewalOffsetSeconds**: nem kötelező. Azon ezredmásodpercek száma, amelyek a token lejárata előtti megújításához szükséges eltolási időszakot határozzák meg. Az alapértelmezett érték 300 ezredmásodperc.

- **navigateFrameWait**: nem kötelező. Azon ezredmásodpercek száma, amely a várakozási időt állítja be a rejtett iframe-elemek célhelyre való navigálása előtt. Az alapértelmezett érték 500 ezredmásodperc.

Ezek csak a MSAL szögletes burkoló könyvtárából érvényesek.
- **unprotectedResources**: nem kötelező.  Nem védett erőforrásokhoz tartozó URI-k tömbje. A MSAL nem csatol jogkivonatot az ezen URI azonosítóval rendelkező kimenő kérelmekhez. Az alapértelmezett érték a `null`.

- **protectedResourceMap**: nem kötelező.  Ez a MSAL által a hozzáférési jogkivonatok webes API-hívásokban való automatikus csatolásához használt hatókörökre való leképezése. A rendszer egyetlen hozzáférési jogkivonatot kapott az erőforráshoz. Így az adott erőforrás elérési útját a következőképpen képezheti le: {"https://graph.microsoft.com/v1.0/me", ["user. Read"]} vagy az erőforrás alkalmazás URL-címe a következőként: {"https://graph.microsoft.com/", ["user. Read", "mail. Send"]}. Ez a CORS-hívásokhoz szükséges. Az alapértelmezett érték a `null`.
