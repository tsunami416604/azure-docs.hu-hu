---
title: "Az Azure AD v2 JS SPA interaktív telepítés - teszt |} Microsoft Docs"
description: "Hogyan JavaScript SPA-alkalmazások Azure Active Directory-v2 végpontja hozzáférési jogkivonatok igénylő API meghívása"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/01/2017
ms.author: andret
ms.openlocfilehash: c888760ab311e8ac08b1e625bb837f91047db645
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
## <a name="test-your-code"></a>Tesztelheti a kódját

> ### <a name="testing-with-visual-studio"></a>A Visual Studio tesztelése
> Amennyiben a Visual Studio használ, nyomja meg az `F5` a projekt futtatásához: a böngészőben megnyílik, és arra utasítja, hogy *http://localhost: {port}* válaszoknál láthatja a *Microsoft Graph API hívása* gombra.

<p/><!-- -->

> ### <a name="testing-with-python-or-another-web-server"></a>A Python vagy egy másik webkiszolgálón tesztelése
> Ha a Visual Studio nem használ, győződjön meg arról, a webkiszolgáló elindult, és a mappa tartalmazó alapján TCP-port figyelésére van konfigurálva a *index.html* fájlt. A Python, megkezdheti a porton figyel futtatásával a parancsban a kérdés / terminál, az alkalmazás mappájában:
> 
> ```bash
> python -m http.server 8080
> ```
>  Ezután nyissa meg a böngészőben, és a típus *8080* vagy *http://localhost: {port}* – Ha a *port* felel meg a portot, amelyet a webkiszolgáló figyel. A tartalmát a index.html lapon kell megjelennie a *Microsoft Graph API hívása* gombra.

## <a name="test-your-application"></a>Az alkalmazás tesztelése

A böngésző után betölti a *index.html*, kattintson a *Microsoft Graph API hívása* gombra. Ha az első alkalommal, a böngésző átirányítja Önt a Microsoft Azure Active Directory v2 végpont, ahová kéri a bejelentkezéshez.
 
![A minta képernyőkép](media/active-directory-singlepageapp-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="consent"></a>Hozzájárulás
Jelentkezzen be az alkalmazás első alkalommal lehetősége lesz az alábbihoz hasonló hozzájárulási képernyő ahol el kell fogadnia:

 ![Hozzájárulás képernyő](media/active-directory-singlepageapp-javascriptspa-test/javascriptspaconsent.png)


### <a name="expected-results"></a>Kívánt eredmény elérése érdekében
Felhasználói profil adatait a Microsoft Graph API-hívás válasz által visszaadott kell megjelennie.
 
 ![Results (Eredmények)](media/active-directory-singlepageapp-javascriptspa-test/javascriptsparesults.png)

A token szerzett alapvető információkat is megjelenik a *Access Token* és *azonosító Token jogcímek* jelölőnégyzetéből.

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>További információ a hatókörök és delegált jogosultságokkal sikeresen telepítették

A Microsoft Graph API megköveteli a `user.read` hatókörrel, hogy a felhasználói profil olvasása. Minden egyes a regisztrációs portál regisztrált alkalmazás alapértelmezés szerint automatikusan megjelenik az ebben a hatókörben. Néhány más Microsoft Graph API, továbbá egyéni API-k, a háttér-kiszolgáló további hatókörökkel lehet szükség. Például a Microsoft Graph-hatóköre `Calendars.Read` listát a felhasználók naptáraiban szükséges. Ahhoz, hogy hozzáférhessen a környezetben, az alkalmazások a felhasználó naptár, hozzá kell adnia a `Calendars.Read` jogosultságot a az alkalmazás regisztrációs adatait, majd adja hozzá a `Calendars.Read` a hatókör a `acquireTokenSilent` hívható meg. A felhasználó kérheti további hozzájárulásokat azoktól a hatókörök számának növelésével.

Ha egy háttér-API nem igényli a hatókör (nem ajánlott), használhatja a `clientId` a hatóköreként a `acquireTokenSilent` és/vagy `acquireTokenRedirect` hívások.

<!--end-collapse-->
