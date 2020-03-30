---
title: Egyéni állapot átadása hitelesítési kérelmekben (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogy miként adható meg egyéni állapotparaméter-érték a hitelesítési kérelemben a Microsoft JavaScript-hitelesítési könyvtár (MSAL.js) használatával.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/16/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1c05956f83ad3a6491627be8916fac2c8be2b7ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084936"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Egyéni állapot áthárítása az MSAL.js használatával történő hitelesítési kérelmekben

Az OAuth 2.0 által meghatározott *állapotparaméter* egy hitelesítési kérelemben is szerepel, és a jogkivonat-válaszban is megjelenik a helyek közötti kérelmek hamisítási támadásainak megakadályozása érdekében. Alapértelmezés szerint a Microsoft JavaScript hitelesítési könyvtára (MSAL.js) *state* egy véletlenszerűen generált egyedi állapotparaméter-értéket ad át a hitelesítési kérelmekben.

Az állapotparaméter az alkalmazás állapotának kódolására is használható az átirányítás előtt. A felhasználó állapotát átadhatja az alkalmazásban, például azt az oldalt vagy nézetet, amelyen voltak, a paraméter bemeneteként. Az MSAL.js könyvtár lehetővé teszi, hogy az `Request` egyéni állapotot állapotparaméterként adja át az objektumban:

```javascript
// Request type
export type AuthenticationParameters = {
    scopes?: Array<string>;
    extraScopesToConsent?: Array<string>;
    prompt?: string;
    extraQueryParameters?: QPDict;
    claimsRequest?: string;
    authority?: string;
    state?: string;
    correlationId?: string;
    account?: Account;
    sid?: string;
    loginHint?: string;
    forceRefresh?: boolean;
};
```

> [!Note]
> Ha ki szeretne hagyni egy gyorsítótárazott jogkivonatot, és a `forceRefresh` kiszolgálóra szeretne menni, adja át a logikai értéket a bejelentkezési/tokenkérelemhez használt AuthenticationParameters objektumba.
> `forceRefresh`nem kell használni alapértelmezés szerint, mert a teljesítmény hatással van az alkalmazásra.
> A gyorsítótárra támaszkodva a felhasználók jobb élményben lesz része.
> A gyorsítótár kihagyása csak olyan esetekben használható, amikor a jelenleg gyorsítótárazott adatok nem rendelkeznek naprakész adatokkal.
> Például egy rendszergazdai eszköz, amely szerepköröket ad hozzá egy felhasználóhoz, amelynek új jogkivonatot kell beszereznie frissített szerepkörökkel.

Példa:

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

Az átadott állapot hozzáfűzi az MSAL.js által beállított egyedi GUID-hoz a kérelem elküldésekor. A válasz visszaadásakor az MSAL.js megkeresi az állapotegyezést, majd visszaadja az `Response` objektumban megadott egyéni értéket `accountState`.

```javascript
export type AuthResponse = {
    uniqueId: string;
    tenantId: string;
    tokenType: string;
    idToken: IdToken;
    accessToken: string;
    scopes: Array<string>;
    expiresOn: Date;
    account: Account;
    accountState: string;
};
```

További információ: [egyoldalas alkalmazás (SPA) létrehozása](scenario-spa-overview.md) az MSAL.js használatával.