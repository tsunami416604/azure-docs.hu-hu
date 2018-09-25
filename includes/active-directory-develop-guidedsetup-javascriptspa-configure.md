---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 07aac49e7aed7c95863a2058a9de3d1e8f2cd1ad
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47060905"
---
## <a name="register-your-application"></a>Alkalmazás regisztrálása

Hozzon létre egy alkalmazást, válassza ki az egyiket több módja is van:

### <a name="option-1-register-your-application-express-mode"></a>1. lehetőség: Regisztrálja az alkalmazást (Expressz mód)
Most kell regisztrálnia az alkalmazást a *Microsoft alkalmazásregisztrációs portálon*:

1.  Az alkalmazás regisztrálása a [Microsoft alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/portal/register-app?appType=singlePageApp&appTech=javascriptSpa&step=configure).
2.  Adjon meg egy nevet az alkalmazás és az e-maileket.
3.  Ellenőrizze, hogy a beállítás **interaktív telepítés** be van jelölve.
4.  Kövesse az utasításokat beszerzéséhez, és illessze be a kódot.

### <a name="option-2-register-your-application-advanced-mode"></a>2. lehetőség: Regisztrálja az alkalmazást (Speciális mód)

1. Nyissa meg a [Microsoft alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/portal/register-app) kell regisztrálni egy alkalmazást.
2. Adjon meg egy nevet az alkalmazás és az e-maileket.
3. Ellenőrizze, hogy a beállítás **interaktív telepítés** nincs bejelölve.
4.  Kattintson a **hozzáadása Platform**, majd **webes**.
5. Adja hozzá a **átirányítási URL-cím** , amely megfelel az alkalmazás URL-címet, a webkiszolgáló alapján. Tekintse át az utasításokat az alábbi szakaszokban és az átirányítási URL-cím a Visual Studio és a csomópont.
6. Kattintson a **Mentés** gombra.

> #### <a name="setting-redirect-url-for-node"></a>A Node beállítás átirányítási URL-cím
> A node.js-ben, beállíthatja a webes kiszolgálóport a *server.js* fájlt. Ebben az oktatóanyagban referencia azonban nyugodtan elérhető más port használata 30662 portot használja. Minden esetben kövesse az alábbi utasításokat egy átirányítási URL-címet az alkalmazás regisztrációs adatainak beállításához:<br/>
> - Váltson vissza a *alkalmazásregisztrációs portálon* és `http://localhost:30662/` , egy `Redirect URL`, vagy használjon `http://localhost:[port]/` Ha egy egyéni TCP-portot használja (ahol *[port]* egyéni TCP-portja szám), és kattintson a "Mentés gombra"

<p/>

> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>A Visual Studio utasításokat az átirányítási URL-cím beszerzése
> Kövesse az alábbi lépéseket az átirányítási URL-cím:
> 1.    A **Megoldáskezelőben**, válassza ki a projektet, és tekintse meg a **tulajdonságok** ablak. Ha nem látja a **tulajdonságok** ablakban, majd nyomja le a **F4**.
> 2.    Másolja az értéket a **URL-cím** a vágólapra:<br/> ![Projekt tulajdonságai](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    Váltson vissza a *alkalmazásregisztrációs portálon* illessze be az értéket, és egy **átirányítási URL-cím** válassza **mentése**


#### <a name="configure-your-javascript-spa"></a>A JavaScript SPA konfigurálása

1.  Az a `index.html` projekt beállítása során létrehozott fájlt, adja hozzá az alkalmazás regisztrációs adatok. Adja meg a következő kódot a felül található a `<script></script>` címkék törzsében a `index.html` fájlt:

```javascript
var applicationConfig = {
    clientID: "[Enter the application Id here]",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
<ol start="3">
<li>
Cserélje le <code>Enter the application Id here</code> az imént regisztrált alkalmazás azonosítójával.
</li>
</ol>
