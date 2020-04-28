---
title: Egyéni állapot továbbítása a hitelesítési kérelmekben (MSAL. js) | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan adhat át egyéni állapotú paramétereket a hitelesítési kérelemben a JavaScripthez készült Microsoft Authentication Library (MSAL. js) használatával.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77084936"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Egyéni állapot továbbítása a hitelesítési kérelmekben a MSAL. js használatával

A OAuth 2,0 által definiált *State* paraméter a hitelesítési kérelem része, és a jogkivonat válaszában is visszaadja, hogy megakadályozza a helyek közötti kérelmek hamisításának elleni támadásokat. Alapértelmezés szerint a Microsoft Authentication Library for JavaScript (MSAL. js) egy véletlenszerűen generált egyedi *állapot* paramétert ad meg a hitelesítési kérelmekben.

Az állapot paraméter az alkalmazás állapotának az átirányítás előtt történő kódolására is használható. Átadhatja a felhasználó állapotát az alkalmazásban, például a lapon vagy a nézetben, a paraméter bemenetének megfelelően. A MSAL. js függvénytár lehetővé teszi, hogy az `Request` objektumon keresztül átadja az egyéni állapot paraméterét:

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
> Ha egy gyorsítótárazott tokent szeretne kihagyni, és a kiszolgálóra lép, adja át a logikai `forceRefresh` értéket a bejelentkezési/jogkivonat-kérelem létrehozásához használt AuthenticationParameters objektumba.
> `forceRefresh`Alapértelmezés szerint nem ajánlott használni az alkalmazás teljesítményére gyakorolt hatás miatt.
> A gyorsítótárra támaszkodva jobb felhasználói élményt biztosíthat a felhasználóknak.
> A gyorsítótár kihagyása csak olyan helyzetekben használható, ahol tudja, hogy a jelenleg gyorsítótárazott adatok nem rendelkeznek naprakész információkkal.
> Például egy felügyeleti eszköz, amely szerepköröket rendel hozzá egy olyan felhasználóhoz, akinek új jogkivonatot kell beszereznie a frissített szerepkörökkel.

Például:

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

A rendszer hozzáfűzi az átadott állapotot a MSAL. js által a kérelem elküldésekor beállított egyedi GUID-azonosítóhoz. A válasz visszaadásakor a MSAL. js ellenőrzi az állapot egyezését, majd az `Response` objektumban lévő egyéni átadott állapotot adja `accountState`vissza.

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

További tudnivalókért tekintse meg az [egyoldalas alkalmazások (Spa)](scenario-spa-overview.md) MSAL. js használatával történő létrehozását ismertető témakört.