---
title: Egyéni állapot továbbítása a hitelesítési kérelmekben (MSAL. js) | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan adhat át egyéni állapotú paramétereket a hitelesítési kérelemben a JavaScripthez készült Microsoft Authentication Library (MSAL. js) használatával.
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/16/2020
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: be2dd887358aa00c87a4b5668a99c425d83b59dc
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76696027"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Egyéni állapot továbbítása a hitelesítési kérelmekben a MSAL. js használatával

A OAuth 2,0 által definiált *State* paraméter a hitelesítési kérelem része, és a jogkivonat válaszában is visszaadja, hogy megakadályozza a helyek közötti kérelmek hamisításának elleni támadásokat. Alapértelmezés szerint a Microsoft Authentication Library for JavaScript (MSAL. js) egy véletlenszerűen generált egyedi *állapot* paramétert ad meg a hitelesítési kérelmekben.

Az állapot paraméter az alkalmazás állapotának az átirányítás előtt történő kódolására is használható. Átadhatja a felhasználó állapotát az alkalmazásban, például a lapon vagy a nézetben, a paraméter bemenetének megfelelően. A MSAL. js függvénytár lehetővé teszi, hogy az egyéni állapot paramétert átadja a `Request` objektumban:

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
> Ha egy gyorsítótárazott tokent szeretne kihagyni, és a kiszolgálóra lép, adja át a logikai `forceRefresh` a bejelentkezési/jogkivonat-kérelem létrehozásához használt AuthenticationParameters objektumba.
> a `forceRefresh` nem használható alapértelmezés szerint az alkalmazás teljesítményére gyakorolt hatás miatt.
> A gyorsítótárra támaszkodva jobb felhasználói élményt biztosíthat a felhasználóknak.
> A gyorsítótár kihagyása csak olyan helyzetekben használható, ahol tudja, hogy a jelenleg gyorsítótárazott adatok nem rendelkeznek naprakész információkkal.
> Például egy felügyeleti eszköz, amely szerepköröket rendel hozzá egy olyan felhasználóhoz, akinek új jogkivonatot kell beszereznie a frissített szerepkörökkel.

Példa:

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

A rendszer hozzáfűzi az átadott állapotot a MSAL. js által a kérelem elküldésekor beállított egyedi GUID-azonosítóhoz. A válasz visszaadásakor a MSAL. js ellenőrzi az állapot egyezését, majd visszaadja a `Response` objektumban lévő egyéni átadott állapotot `accountState`ként.

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