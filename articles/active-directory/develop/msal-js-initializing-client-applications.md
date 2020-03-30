---
title: MSAL.js ügyfélalkalmazások inicializálása | Azure
titleSuffix: Microsoft identity platform
description: Információ az ügyfélalkalmazások inicializálásáról a Microsoft Authentication Library for JavaScript (MSAL.js) használatával.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/12/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: a0a2c5fc971c3f1f3283d95c5617bdf1e88a6a58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084043"
---
# <a name="initialize-client-applications-using-msaljs"></a>Ügyfélalkalmazások inicializálása az MSAL.js használatával
Ez a cikk a Microsoft Authentication Library for JavaScript (MSAL.js) inicializálását ismerteti egy felhasználói ügynökalkalmazás példányával. A user-agent alkalmazás a nyilvános ügyfélalkalmazás egy olyan formája, amelyben az ügyfélkód végrehajtása egy felhasználói ügynökben, például egy webböngészőben történik. Ezek az ügyfelek nem tárolnak titkos kulcsokat, mivel a böngésző környezete nyíltan elérhető. Ha többet szeretne megtudni az ügyfélalkalmazás típusairól és az alkalmazás konfigurációs beállításairól, olvassa el az [áttekintést.](msal-client-applications.md)

## <a name="prerequisites"></a>Előfeltételek
Egy alkalmazás inicializálása előtt először regisztrálnia kell [azt az Azure Portalon,](scenario-spa-app-registration.md) hogy az alkalmazás integrálható legyen a Microsoft identitásplatformmal. A regisztráció után a következő információkra lehet szüksége (amelyek az Azure Portalon találhatók):

- Az ügyfélazonosító (az alkalmazás GUID azonosítóját képviselő karakterlánc)
- Az identitásszolgáltató URL-címe (a példány neve) és az alkalmazás bejelentkezési közönsége. Ezt a két paramétert együttesen hatóságnak nevezzük.
- A bérlői azonosító, ha egy üzletági alkalmazást kizárólag a szervezet (más néven egybérlős alkalmazás) számára ír.
- A webalkalmazások esetében be kell állítania az átirányítási uri-t is, ahol az identitásszolgáltató visszatér az alkalmazáshoz a biztonsági jogkivonatokkal.

## <a name="initializing-applications"></a>Alkalmazások inicializálása

Az MSAL.js az alábbiak szerint használható egy egyszerű JavaScript/Typescript alkalmazásban. A MSAL hitelesítési környezet inicializálása egy konfigurációs objektummal történő példányosítással. `UserAgentApplication` A MSAL.js inicializálásához szükséges minimális konfiguráció az alkalmazás ügyfélazonosítója, amelyet az alkalmazásregisztrációs portálról kell beszereznie.

Az átirányítási folyamatokkal`loginRedirect` (és `acquireTokenRedirect`) rendelkező hitelesítési módszerek esetében explicit módon `handleRedirectCallback()` kell regisztrálnia egy visszahívást a sikeres vagy a metóduson keresztüli hiba érdekében. Erre azért van szükség, mert az átirányítási folyamatok nem adnak vissza ígéreteket, mint az előugró felületű módszerek.

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

Az MSAL.js célja, hogy egyetlen példánysal és konfigurációval `UserAgentApplication` rendelkezik, hogy egyetlen hitelesítési környezetet képviseljen. Több példány használata nem ajánlott, mivel ütköző gyorsítótár-bejegyzéseket és viselkedést okoznak a böngészőben.

## <a name="configuration-options"></a>Beállítási lehetőségek

Az MSAL.js program az alábbi konfigurációs objektummal rendelkezik, amely `UserAgentApplication`konfigurálható beállításokat biztosít a példány létrehozásához.

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

Az alábbiakban a konfigurációs objektumban jelenleg támogatott konfigurálható beállítások teljes készlete látható:

- **clientID**: Kötelező. Az alkalmazás ügyfélazonosítója, ezt az alkalmazásregisztrációs portálról kell beszereznie.

- **hatóság**: Nem kötelező. Olyan URL-cím, amely egy olyan könyvtárat jelez, amelytől az MSAL jogkivonatokat kérhet. Az alapértelmezett `https://login.microsoftonline.com/common`érték a következő: .
    * Az Azure AD-ben az&lt;űrlap&gt;/&lt;&gt;https:// &lt;példány&gt; közönsége, ahol példány az `https://login.microsoftonline.com`identitásszolgáltató tartomány (például ) és &lt;a közönség&gt; egy azonosító, amely a bejelentkezési közönséget jelöli. Ez a következő értékek lehetnek:
        * `https://login.microsoftonline.com/<tenant>`- a bérlő a bérlőhöz társított tartomány, például contoso.onmicrosoft.com, vagy a címtár `TenantID` tulajdonságát képviselő GUID, amely csak egy adott szervezet felhasználóinak bejelentkezéséhez használatos.
        * `https://login.microsoftonline.com/common`- Munkahelyi és iskolai fiókkal vagy Microsoft-személyes fiókkal rendelkező felhasználók bejelentkezéséhez használható.
        * `https://login.microsoftonline.com/organizations/`- Munkahelyi és iskolai fiókkal rendelkező felhasználók jelentkezéséhez.
        * `https://login.microsoftonline.com/consumers/`- Használt bejelentkezni a felhasználók csak a személyes Microsoft-fiók (élő).
    * Az Azure AD B2C,ez `https://<instance>/tfp/<tenant>/<policyName>/`az űrlap, ahol példányaz Azure AD B2C tartomány, azaz {your-tenant-name}.b2clogin.com, bérlő az Azure AD B2C bérlő neve, azaz {your-tenant-name}.onmicrosoft.com, policyName a neve a B2C házirend et alkalmazni.


- **validateAuthority**: Nem kötelező.  Ellenőrizze a tokenek kibocsátóját. Az alapértelmezett szint a `true`. A B2C-alkalmazások esetében, mivel a hatóság értéke ismert, és házirendenként eltérő lehet, `false`a hatóság-érvényesítés nem fog működni, és a értékre kell állítani.

- **redirectUri**: Nem kötelező.  Az alkalmazás átirányítási URI-ja, ahol az alkalmazás hitelesítési válaszokat küldhet és fogadhat. Pontosan meg kell egyeznie a portálon regisztrált átirányítási URI-k egyikével. Alapértelmezés szerint `window.location.href`a.

- **postLogoutRedirectUri**: Nem kötelező.  A kijelentkezés `postLogoutRedirectUri` után átirányítja a felhasználót. Az alapértelmezett `redirectUri`érték a .

- **navigateToLoginRequestUrl**: Nem kötelező. A bejelentkezés után ki lehet kapcsolni az alapértelmezett navigációt a kezdőlapról. Alapértelmezett érték: true (igaz). Ez csak átirányítási folyamatokhoz használatos.

- **cacheLocation**: Nem kötelező.  A böngésző tárhelyét vagy `localStorage` a ikonra állítja. `sessionStorage` A mező alapértelmezett értéke: `sessionStorage`.

- **storeAuthStateInCookie**: Nem kötelező.  Ez a jelző az MSAL.js v0.2.2-ben került bevezetésre a Microsoft Internet Explorer és a Microsoft Edge [hitelesítési ciklusával kapcsolatos problémák](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones) javításaként. Engedélyezze, `storeAuthStateInCookie` hogy a jelző igaz legyen, hogy kihasználhassa ezt a javítást. Ha ez engedélyezve van, az MSAL.js tárolja a hitelesítő kérelem állapotát, amely a böngésző cookie-iban az auth folyamatok érvényesítéséhez szükséges. Alapértelmezés szerint ez a `false`jelző a értékre van állítva.

- **logger**: Nem kötelező.  A naplózó objektum egy visszahívási példány, amely a fejlesztő által biztosítható a naplók felhasználásához és közzétételéhez egyéni módon. Az áthaladó naplózó objektummal kapcsolatos részletekért lásd [a naplózást az msal.js fájlban.](msal-logging.md)

- **loadFrameTimeout:** Nem kötelező.  Az Azure AD-ből érkező jogkivonat-megújítási válasz előtti inaktivitás ezredmásodperces számát időtúltöltésnek kell tekinteni. Az alapértelmezett érték 6 másodperc.

- **tokenRenewalOffsetSeconds**: Nem kötelező. A token megújításához szükséges eltolási ablakot a lejárat előtt beállító ezredmásodpercek száma. Az alapértelmezett érték 300 ezredmásodperc.

- **navigateFrameWait**: Nem kötelező. Az ezredmásodpercek száma, amely beállítja a várakozási időt, mielőtt a rejtett iframe-ek elnavigálnának a célhelyükre. Az alapértelmezett érték 500 ezredmásodperc.

Ezek csak az MSAL Angular burkolókönyvtárból öröklődhetnek:
- **unprotectedResources**: Nem kötelező.  Nem védett erőforrásokat tartalmazó URI-k tömbje. Az MSAL nem csatol jogkivonatot az uri-val rendelkező kimenő kérelmekhez. Alapértelmezés szerint `null`a.

- **protectedResourceMap**: Nem kötelező.  Ez az erőforrások leképezése az MSAL által használt hatókörökhez a webes API-hívások hozzáférési jogkivonatainak automatikus csatolásához. Az erőforráshoz egyetlen hozzáférési jogkivonat kerül beszerzésre. Így egy adott erőforrás elérési útját ahttps://graph.microsoft.com/v1.0/mekövetkezőképpen képezheti le: {" ", ["user.read"]]}, vagy az erőforrás alkalmazás URL-címe: {"https://graph.microsoft.com/", ["user.read", "mail.send"]}. Ez cors hívások esetén szükséges. Alapértelmezés szerint `null`a.
