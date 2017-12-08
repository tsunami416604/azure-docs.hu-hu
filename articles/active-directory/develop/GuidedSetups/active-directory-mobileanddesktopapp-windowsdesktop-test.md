---
title: "Az Azure AD v2 Windows asztali első lépések – tesztelése |} Microsoft Docs"
description: "Hogyan Windows asztali .NET (XAML) alkalmazások Azure Active Directory-v2 végpontja hozzáférési jogkivonatok igénylő API meghívása"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: e9fbfc301fb987c72605f8b16a707513661a1b65
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
## <a name="test-your-code"></a>Tesztelheti a kódját

Az alkalmazás teszteléséhez nyomja le az `F5` a projektet a Visual Studio futtatásához. A fő ablak kell megjelennie:

![A minta képernyőkép](media/active-directory-mobileanddesktopapp-windowsdesktop-test/samplescreenshot.png)

Amikor készen áll a tesztelése, kattintson *Microsoft Graph API hívása* és a Microsoft Azure Active Directory (szervezeti fiók) vagy egy Microsoft Account (live.com, outlook.com) fiók használatával jelentkezzen be. Az első, az látni fogja kérni a felhasználót, hogy jelentkezzen be egy ablakban:

![bejelentkezés](media/active-directory-mobileanddesktopapp-windowsdesktop-test/signinscreenshot.png)

### <a name="consent"></a>Hozzájárulás
Az első alkalommal bejelentkezik az alkalmazás választhat hasonló hozzájárulási képernyő a alatt, ahol kell explicit módon fogadja el:

![Hozzájárulás képernyő](media/active-directory-mobileanddesktopapp-windowsdesktop-test/consentscreen.png)

### <a name="expected-results"></a>Kívánt eredmény elérése érdekében
Felhasználói profil adatait a API-hívási eredmények képernyőn a Microsoft Graph API-hívás által visszaadott kell megjelennie.

Emellett meg kell jelennie a token keresztül szerzett alapvető információkat `AcquireTokenAsync` vagy `AcquireTokenSilentAsync` a Token adatait mezőbe:

|Tulajdonság  |Formátumban  |Leírás |
|---------|---------|---------|
|Név | {Felhasználó teljes neve} |A felhasználó nagyapja vezeték- és keresztneve|
|Felhasználónév |<span>user@domain.com</span> |A felhasználó azonosítására használt felhasználónév|
|Jogkivonat lejár |{DateTime}         |Az az idő, amikor a jogkivonat lejár. MSAL lesz a lejárati dátum meghosszabbításához, szükség esetén a jogkivonatban megújításával|
|hozzáférési jogkivonat |{Karakterlánc}         |A lexikális elem karakterlánca küldött, küld egy engedélyezési fejléc igénylő HTTP-kérelmek|

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>További információ a hatókörök és delegált jogosultságokkal sikeresen telepítették
Graph API megköveteli a `user.read` hatókörrel, hogy a felhasználói profil olvasása. Minden egyes a regisztrációs portál regisztrált alkalmazás alapértelmezés szerint automatikusan megjelenik az ebben a hatókörben. Néhány egyéb Graph API-k, valamint a háttérkiszolgáló az egyéni API-k szükséges további hatókörökkel. Például a diagramot `Calendars.Read` lista felhasználók naptáraiban szükséges. A felhasználó naptár kontextusban az alkalmazás eléréséhez, hozzá kell adnia `Calendars.Read` meghatalmazott alkalmazás regisztrációs adatait, majd adja hozzá `Calendars.Read` számára a `AcquireTokenAsync` hívható meg. Felhasználó kérheti további hozzájárulásokat azoktól a hatókörök számának növelésével.

<!--end-collapse-->

[!INCLUDE  [Help and Support Options](../../../../includes/active-directory-develop-help-support-include.md)]