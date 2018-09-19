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
ms.openlocfilehash: 9817e94ba77c83b8620274ba41f9d862b6a5ce6d
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46293602"
---
## <a name="test-your-code"></a>Tesztelheti a kódját

### <a name="test-with-visual-studio"></a>A Visual Studióval tesztelése
Ha a Visual Studio használata esetén nyomja le az **F5** , futtassa a projektet. A böngésző megnyitja a http://<span></span>localhost: {port} helyen, és tekintse meg a **Microsoft Graph API meghívása** gombra.

<p/><!-- -->

### <a name="test-with-node-or-other-web-server"></a>Csomópont- vagy egyéb webkiszolgáló tesztelése
Ha nem használ a Visual Studióban, győződjön meg arról, a webalkalmazás-kiszolgáló elindult. Konfigurálja a kiszolgálót helye alapján TCP-portra figyeljen a **index.html** fájlt. A Node indítsa el a webkiszolgáló figyelésére, a port a következő parancsok futtatásával egy parancssort az alkalmazás mappájában:

```bash
npm install
node server.js
```
Nyissa meg a böngészőt, és írja be a http://<span></span>localhost:30662 vagy a http://<span></span>localhost: {port} ahol **port** a kiszolgáló figyel a portot. Megjelenik az index.html fájlt, és a **Microsoft Graph API meghívása** gombra.

## <a name="test-your-application"></a>Az alkalmazás tesztelése

Miután a böngészőben a index.html fájlt betölt, válassza ki **Microsoft Graph API meghívása**. Az első alkalommal futtatja az alkalmazást, a böngésző átirányítja Önt a Microsoft Azure Active Directory (Azure AD) v2.0-végpont, és kéri, hogy jelentkezzen be:

![Jelentkezzen be a JavaScript SPA-fiók](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="provide-consent-for-application-access"></a>Alkalmazás-hozzáférési hozzájárulás megadása

Első alkalommal jelentkezik be az alkalmazás kéri, hogy Ön hozzájárul ahhoz, hogy az alkalmazás eléréséhez a profil és a bejelentkezéshez adja meg:

![Adja meg az Ön hozzájárul az alkalmazás-hozzáférés](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Kérelem eredményének megtekintése
Miután bejelentkezett, megjelenik a felhasználói profil adatait a **Graph API-hívás válasz** mezőbe.
 
![A Microsoft Graph API-hívás a várt eredmények](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

Is meg kell jelennie a jogkivonatot a megszerzett alapvető adatait a **Access Token** és **azonosító jogkivonat jogcímek** mezőkbe.

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>További információ a hatókörök és delegált engedélyek

A Microsoft Graph API megköveteli a **user.read** hatókörrel, hogy a felhasználói profil olvasása. Minden alkalmazás, amely regisztrálva van az alkalmazásregisztrációs portálon alapértelmezés szerint automatikusan megjelenik az ebben a hatókörben. Egyéb Microsoft Graph API-k, valamint a egyéni API-k, a háttér-kiszolgáló további hatókörökkel lehet szükség. A Microsoft Graph API megköveteli a **Calendars.Read** hatókörrel, hogy a felhasználók naptáraiban listázása.

A felhasználók naptáraiban keretén belül egy alkalmazás eléréséhez, adja hozzá a **Calendars.Read** delegált engedéllyel az alkalmazás regisztrációs adatok. Adja hozzá a **Calendars.Read** a hatókört a **acquireTokenSilent** hívja. 

>[!NOTE]
>A felhasználó további címtárbérlőhöz hatókörök számának növelésével kérheti.

Ha egy háttérrendszeri API nem igényel a hatókör (nem ajánlott), akkor használhatja a **clientId** a hatókörként a **acquireTokenSilent** és **acquireTokenRedirect** hívások.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
