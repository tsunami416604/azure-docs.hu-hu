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
ms.date: 05/29/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4cb0f3d054f9afd0c606f80fd6fc5d553eff806
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74916315"
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
};
```

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