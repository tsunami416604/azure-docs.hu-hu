---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 494f5fb6f2bfdec86cad01a37e57c3ec219a77f9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133235"
---
## <a name="test-your-code"></a>Tesztelheti a kódját

Tesztelheti a kódját a következő környezetekben egyikével.

### <a name="test-with-nodejs"></a>Tesztelje a node.js használatával

Ha nem használ a Visual Studióban, győződjön meg arról, a webalkalmazás-kiszolgáló elindult.

1. Konfigurálja a kiszolgálót helye alapján TCP-portra figyeljen a *index.html* fájlt. A node.js-hez indítsa el a webkiszolgáló figyelésére, a port a következő parancsok futtatásával egy parancssort az alkalmazás mappából:

    ```bash
    npm install
    node server.js
    ```
1. Adja meg a böngészőben, **http://\<span >\</span > localhost:30662** vagy **http://\<span >\</span > localhost: {port}** , ahol *port* a kiszolgáló figyel a portot. Látnia kell a tartalmát a *index.html* fájlt, és a **bejelentkezés** gombra.

### <a name="test-with-visual-studio"></a>A Visual Studióval tesztelése

Ha a Visual Studio használata esetén válassza ki a projekt megoldás, és válassza le az F5 billentyűt a projekt futtatása. A böngésző megnyitja a http://<span></span>localhost: {port} helyen, és a **bejelentkezés** gomb láthatóvá tenni.

## <a name="test-your-application"></a>Az alkalmazás tesztelése

A böngésző után betöltődik a *index.html* fájlt, jelölje be **bejelentkezés**. Jelentkezzen be a Microsoft identity platform végpont kéri:

![A JavaScript SPA fiók bejelentkezési ablak](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Alkalmazás-hozzáférési hozzájárulás megadása

Első alkalommal jelentkezik be az alkalmazás kéri, hogy hozzáférést, a profil, és a bejelentkezés:

![A "Engedélyeket kért" ablak](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Kérelem eredményének megtekintése

Miután bejelentkezett, a Microsoft Graph API-válasz, amely megjelenik az oldalon a felhasználói profil adatait adja vissza.

![A Microsoft Graph API-hívás eredményei](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>További információ a hatókörök és delegált engedélyek

A Microsoft Graph API megköveteli a *user.read* hatókörrel, hogy a felhasználói profil olvasása. Minden alkalmazás, amely regisztrálva van az alkalmazásregisztrációs portálon alapértelmezés szerint automatikusan megjelenik az ebben a hatókörben. Egyéb Microsoft Graph API-k, valamint a egyéni API-k, a háttér-kiszolgáló további hatókörökkel lehet szükség. Ha például a Microsoft Graph API megköveteli a *Calendars.Read* hatókörrel, hogy a felhasználók naptáraiban listázása.

A felhasználók naptáraiban keretén belül egy alkalmazás eléréséhez, adja hozzá a *Calendars.Read* delegált engedéllyel az alkalmazás regisztrációs adatok. Adja hozzá a *Calendars.Read* a hatókört a `acquireTokenSilent` hívja.

>[!NOTE]
>A felhasználó további címtárbérlőhöz hatókörök számának növelésével kérheti.

Ha a háttérrendszeri API nem igényel a hatókör (nem ajánlott), használhatja a *clientId* a tokenek beszerzésére hívásokat hatókörként.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
