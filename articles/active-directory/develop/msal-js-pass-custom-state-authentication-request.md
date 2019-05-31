---
title: A hitelesítési kérések (Microsoft-hitelesítési tár JavaScript-) egyéni állapotát adja át |} Az Azure
description: Ismerje meg, hogyan adhatók át egy egyéni Állapot paraméter értéke a használatával a Microsoft-hitelesítési tár (MSAL.js) JavaScript-hitelesítési kérelmet.
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
ms.date: 05/29/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f29d84838ddb11ac359d7a04dbce8e39dd05ac01
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420498"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Adja át a hitelesítési kérések MSAL.js használatával egyéni állapota
A *állapot* paramétert, ahogyan az OAuth 2.0, a hitelesítési kérelmet szerepel, és webhelyközi kérések hamisításának megakadályozása támadások megelőzése érdekében a token válaszban visszaadott is. Alapértelmezés szerint a Microsoft Authentication Library for JavaScript rendszerhez (MSAL.js) átadja egy véletlenszerűen létrehozott egyedi *állapot* paraméter értéke az a hitelesítési kérelmeket.

A state paraméterben is használható információk átirányítási előtt az alkalmazás állapotának kódolása. A felhasználói állapot az alkalmazásban, például az oldal vagy nézet, ez a paraméter bemeneteként voltak adhat át. Az MSAL.js library lehetővé teszi, hogy, hogy adja át az egyéni állapotáról, mint az Állapot paraméter az `Request` objektum:

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
};
```

Példa:

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: “page_url”
}

myMSALObj.loginPopup(loginRequest);
```

Az átadott állapot a rendszer hozzáfűzi az MSAL.js állította, a kérés küldésekor egyedi GUID Azonosítót. Ha a válasz, MSAL.js állapot egyezést ellenőrzi, és adja vissza az egyéni, az állapota sikeres a `Response` az objektum `accountState`.

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

További tudnivalókért olvassa el [egy egyoldalas alkalmazás (SPA) felépítésére](scenario-spa-overview.md) MSAL.js használatával.