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
ms.openlocfilehash: bb73f58c5dca5e49cdb075b046f883ffeb77c95c
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203319"
---
## <a name="test-your-code"></a>Tesztelheti a kódját

### <a name="test-with-node"></a>A csomópont tesztelése

Ha nem használ a Visual Studióban, győződjön meg arról, a webalkalmazás-kiszolgáló elindult.

1. Konfigurálja a kiszolgálót helye alapján TCP-portra figyeljen a **index.html** fájlt. A Node indítsa el a webkiszolgáló figyelésére, a port a következő parancsok futtatásával egy parancssort az alkalmazás mappájában:

    ```bash
    npm install
    node server.js
    ```
1. Nyissa meg a böngészőt, és írja be a http://<span></span>localhost:30662 vagy a http://<span></span>localhost: {port} ahol **port** a kiszolgáló figyel a portot. Megjelenik az index.html fájlt, és a **bejelentkezés** gombra.

<p/><!-- -->

### <a name="test-with-visual-studio"></a>A Visual Studióval tesztelése

Ha a Visual Studio használata esetén ügyeljen arra, hogy válassza ki a projekt megoldás, és nyomja meg **F5** , futtassa a projektet. A böngésző megnyitja a http://<span></span>localhost: {port} helyen, és tekintse meg a **bejelentkezés** gombra.

## <a name="test-your-application"></a>Az alkalmazás tesztelése

Kattintson a böngésző az index.html fájlt betölt, miután **bejelentkezés**. A rendszer felkéri jelentkezzen be a Microsoft Azure Active Directory (Azure AD) v2.0-végpont:

![Jelentkezzen be a JavaScript SPA-fiók](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Alkalmazás-hozzáférési hozzájárulás megadása

Első alkalommal jelentkezik be az alkalmazás kéri, hogy Ön hozzájárul ahhoz, hogy az alkalmazás eléréséhez a profil és a bejelentkezéshez adja meg:

![Adja meg az Ön hozzájárul az alkalmazás-hozzáférés](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Kérelem eredményének megtekintése

Ha a bejelentkezéshez, akkor jelenik meg a felhasználói profil adatait a Microsoft Graph API-válasz megjelenik az oldalon.

![A Microsoft Graph API-hívás a várt eredmények](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>További információ a hatókörök és delegált engedélyek

A Microsoft Graph API megköveteli a `user.read` hatókörrel, hogy a felhasználói profil olvasása. Minden alkalmazás, amely regisztrálva van az alkalmazásregisztrációs portálon alapértelmezés szerint automatikusan megjelenik az ebben a hatókörben. Egyéb Microsoft Graph API-k, valamint a egyéni API-k, a háttér-kiszolgáló további hatókörökkel lehet szükség. Ha például a Microsoft Graph API megköveteli a `Calendars.Read` hatókörrel, hogy a felhasználók naptáraiban listázása.

A felhasználók naptáraiban keretén belül egy alkalmazás eléréséhez, adja hozzá a `Calendars.Read` delegált engedéllyel az alkalmazás regisztrációs adatok. Adja hozzá a `Calendars.Read` a hatókört a `acquireTokenSilent` hívja.

>[!NOTE]
>A felhasználó további címtárbérlőhöz hatókörök számának növelésével kérheti.

Ha a háttérrendszeri API nem igényel a hatókör (nem ajánlott), használhatja a `clientId` a tokenek beszerzésére hívásokat hatókörként.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]