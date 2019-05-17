---
title: Interaktív kérelmek (Microsoft-hitelesítési tár JavaScript-) viselkedés kérni |} Az Azure
description: Ismerje meg az interaktív hívás használatával a Microsoft-hitelesítési tár (MSAL.js) JavaScript-parancssor viselkedése testreszabása.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd0d736345f312f1a1d6f8f029b41429a3e5f0a7
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544274"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>Az interaktív kérelmek MSAL.js parancssor viselkedése

Amikor egy felhasználó több felhasználói fiókot AD munkamenetet hozott létre egy aktív Azure, az Azure AD bejelentkezési oldal lesz alapértelmezés szerint kéri a felhasználót, jelentkezzen be a folytatás előtt válassza ki a fiókot. Felhasználók nem fogják látni egy fiókválasztás, ha nincs az Azure AD csak egyetlen hitelesített munkamenet.

Az MSAL.js library (v0.2.4 kezdődően) nem küld egy prompt paraméter az interaktív kérelem során (`loginRedirect`, `loginPopup`, `acquireTokenRedirect` és `acquireTokenPopup`), és ezáltal nem kényszeríti ki bármely parancssor viselkedése. A használatával beavatkozás nélküli jogkivonat-kérelmeket a `acquireTokenSilent` metódus MSAL.js átadja egy prompt paraméter `none`.

Alapján a forgatókönyvet, szabályozhatja az az interaktív beállításban a parancssor a kérelem paramétereit a paramétert a módszerek a parancssor viselkedése. Ha például szeretne meghívni a fiók termékválasztó felhasználói felület:

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


A következő parancssor értékeket is át lehet adni az Azure AD-hitelesítés során:

**Bejelentkezés:** Ez az érték a felhasználó megadja hitelesítő adatokat a hitelesítési kérés kényszeríti.

**select_account:** Ez az érték a felhasználó biztosít egy fiók termékválasztó felhasználói felület minden tartozó fiókok listázása a munkamenetben.

**hozzájárulás megadása:** Ezt az értéket meghívják az OAuth hozzájárulási párbeszédet, amely lehetővé teszi a felhasználóknak engedélyt az alkalmazásnak.

**Nincs:** Ez az érték biztosítja, hogy a felhasználó nem látja minden olyan interaktív kérdés. Javasoljuk, hogy nem továbbítja ezt az értéket az MSAL.js interaktív metódusok állhat, de nem várt működést. Ehelyett használja a `acquireTokenSilent` mód csendes hívások eléréséhez.

## <a name="next-steps"></a>További lépések

Tudjon meg többet a `prompt` paramétert a [OAuth 2.0 típusú implicit engedélyezés](v2-oauth2-implicit-grant-flow.md) protokoll melyik MSAL.js könyvtár használja.
