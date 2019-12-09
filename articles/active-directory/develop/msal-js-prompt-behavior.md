---
title: Interaktív kérések (MSAL. js) – gyors működés | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan szabhatja testre a prompt viselkedését az interaktív hívásokban a JavaScripthez készült Microsoft Authentication Library (MSAL. js) használatával.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6654b523cef4623a55bf9f857fd5a723fae2dc47
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74921957"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>A MSAL. js interaktív kéréseinek bekérési viselkedése

Ha egy felhasználó több felhasználói fiókkal rendelkező aktív Azure AD-munkamenetet létesített, az Azure AD bejelentkezési oldala alapértelmezés szerint arra kéri a felhasználót, hogy válasszon egy fiókot a bejelentkezés folytatása előtt. Ha az Azure AD-vel csak egyetlen hitelesített munkamenet van, a felhasználók nem látják a fiók kiválasztási élményét.

A MSAL. js függvénytár (a v 0.2.4-től kezdődően) nem küldi el a prompt paramétert az interaktív kérések során (`loginRedirect`, `loginPopup`, `acquireTokenRedirect` és `acquireTokenPopup`), így nem kényszeríti ki a kérdéses viselkedést. A `acquireTokenSilent` metódust használó csendes jogkivonat-kérelmek esetén a MSAL. js egy prompt paramétert ad meg, amely a `none`értékre van beállítva.

Az alkalmazás forgatókönyve alapján szabályozhatja az interaktív kérések viselkedését úgy, hogy a metódusnak átadott kérési paraméterekben a prompt paramétert állítja be. Ha például meg szeretné hívni a fiók kiválasztási élményét:

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


Az Azure AD-vel való hitelesítéskor a következő prompt értékek adhatók át:

**Bejelentkezés:** Ez az érték kényszeríti a felhasználót, hogy adja meg a hitelesítő adatokat a hitelesítési kérelemben.

**select_account:** Ez az érték megadja a felhasználónak a fiók kiválasztási élményét, amely felsorolja az összes fiókot a munkamenetben.

**beleegyezik:** Ez az érték meghívja a OAuth-jóváhagyással kapcsolatos párbeszédet, amely lehetővé teszi, hogy a felhasználók engedélyeket adjanak az alkalmazásnak.

**nincs:** Ez az érték biztosítja, hogy a felhasználó nem lát interaktív üzenetet. Ezt az értéket nem ajánlott a MSAL. js interaktív módszereire átadni, mert nem várt viselkedéssel rendelkezhet. Ehelyett használja a `acquireTokenSilent` metódust a csendes hívások eléréséhez.

## <a name="next-steps"></a>Következő lépések

További információ a [OAuth 2,0 implicit engedélyezési](v2-oauth2-implicit-grant-flow.md) protokoll `prompt` paraméteréről, amelyet a MSAL. js függvénytár használ.
