---
title: Interaktív kérésprompt viselkedése (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg a gyors viselkedés testreszabását az interaktív hívásokban a Microsoft Authentication Library for JavaScript (MSAL.js) használatával.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 778e89655019a49a30904fbe8d8e6aedf1833e9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76695976"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>Azonnali viselkedés az MSAL.js interaktív kérésekben

Ha egy felhasználó létrehozott egy aktív Azure AD-munkamenet több felhasználói fiókkal, az Azure AD bejelentkezési lap alapértelmezés szerint kérni fogja a felhasználót, hogy válasszon ki egy fiókot, mielőtt a bejelentkezéshez. A felhasználók nem fognak látni egy fiókkiválasztási élményt, ha csak egyetlen hitelesített munkamenet van az Azure AD-vel.

Az MSAL.js függvénytár (a v0.2.4-es értéktől kezdve) `loginPopup` `acquireTokenRedirect` nem `acquireTokenPopup`küld gyors paramétert az interaktív kérések (`loginRedirect`, és a ) során, és így nem kényszerít azonnali viselkedést. A metódust használó `acquireTokenSilent` néma tokenkérelmek esetén az MSAL.js egy prompt paramétert ad át a számára. `none`

Az alkalmazásforgatókönyv alapján szabályozhatja az interaktív kérelmek gyors viselkedését, ha a metódusok számára megadott kérésparaméterekben beállítja a prompt paramétert. Ha például meg szeretné hívni a fiókkijelölési felületet:

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


Az Azure AD-vel való hitelesítéskor a következő gyorsértékek adhatók meg:

**bejelentkezés:** Ez az érték arra kényszeríti a felhasználót, hogy adja meg a hitelesítő adatokat a hitelesítési kérelemben.

**select_account:** Ez az érték a munkamenet összes fiókját felsoroló fiókkijelölési élményt nyújt a felhasználónak.

**hozzájárulás:** Ez az érték az OAuth hozzájárulási párbeszédet fogja meghívni, amely lehetővé teszi a felhasználók számára, hogy engedélyeket adjanak az alkalmazásnak.

**nincs:** Ez az érték biztosítja, hogy a felhasználó ne jelenjenek meg interaktív kérdés. Javasoljuk, hogy ne adja át ezt az értéket az MSAL.js interaktív metódusainak, mivel nem várt viselkedések lehetnek. Ehelyett használja `acquireTokenSilent` a módszert a néma hívások eléréséhez.

## <a name="next-steps"></a>További lépések

További információ `prompt` az MSAL.js függvény által használt [OAuth 2.0 implicit támogatási](v2-oauth2-implicit-grant-flow.md) protokoll paraméteréről.
