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
ms.openlocfilehash: 778e89655019a49a30904fbe8d8e6aedf1833e9a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76695976"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>A MSAL. js interaktív kéréseinek bekérési viselkedése

Ha egy felhasználó több felhasználói fiókkal rendelkező aktív Azure AD-munkamenetet létesített, az Azure AD bejelentkezési oldala alapértelmezés szerint arra kéri a felhasználót, hogy válasszon egy fiókot a bejelentkezés folytatása előtt. Ha az Azure AD-vel csak egyetlen hitelesített munkamenet van, a felhasználók nem látják a fiók kiválasztási élményét.

A MSAL. js függvénytár (a v 0.2.4-től kezdve) nem küldi el a prompt paramétert az`loginRedirect`interaktív `loginPopup`kérések (, `acquireTokenRedirect` és `acquireTokenPopup`) során, így nem kényszeríti ki a kérdéses viselkedést. A `acquireTokenSilent` metódust használó csendes jogkivonat-kérelmek esetén a MSAL. js átadja a következőre beállított prompt paramétert: `none`.

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

## <a name="next-steps"></a>További lépések

További információ a `prompt` [OAuth 2,0 implicit engedélyezési](v2-oauth2-implicit-grant-flow.md) protokoll paraméteréről, amelyet a MSAL. js függvénytár használ.
