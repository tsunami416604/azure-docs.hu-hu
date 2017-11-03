---
title: Az Azure AD v2 iOS Getting Started - teszt |} Microsoft Docs
description: "Hogyan iOS (Swift) alkalmazások Azure Active Directory-v2 végpontja hozzáférési jogkivonatok igénylő API meghívása"
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
ms.date: 05/09/2017
ms.author: andret
ms.openlocfilehash: 4a88096d2b0a23708acdbc1798eac528599b4f71
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
## <a name="test-querying-the-microsoft-graph-api-from-your-ios-application"></a>Lekérdezi a Microsoft Graph API az iOS-alkalmazás tesztelése

Nyomja le az `Command`  +  `R` a kód-szimulátorban történő futtatásához.

![A minta képernyőkép](media/active-directory-mobileanddesktopapp-ios-test/iostestscreenshot.png)

Ha elkészült, teszteléséhez, koppintson *"Hívható meg Microsoft Graph API"* és a felhasználónév és a jelszó megadását kéri.

### <a name="consent"></a>Hozzájárulás
Az első alkalommal bejelentkezik az alkalmazás választhat hasonló hozzájárulási képernyő a alatt, ahol kell explicit módon fogadja el:

![Hozzájárulás képernyő](media/active-directory-mobileanddesktopapp-ios-test/iosconsentscreen.png)

### <a name="expected-results"></a>Kívánt eredmény elérése érdekében
Felhasználói profil által visszaadott adatok a Microsoft Graph API-hívás kell megjelennie a *naplózás* szakasz.

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>További információ a hatókörök és delegált jogosultságokkal sikeresen telepítették

A Microsoft Graph API megköveteli a `user.read` hatókörrel, hogy a felhasználói profil olvasása. Minden egyes a regisztrációs portál regisztrált alkalmazás alapértelmezés szerint automatikusan megjelenik az ebben a hatókörben. Néhány más Microsoft Graph API, továbbá egyéni API-k, a háttér-kiszolgáló további hatókörökkel lehet szükség. Például a Microsoft Graph-hatóköre `Calendars.Read` listát a felhasználók naptáraiban szükséges. A felhasználó naptár kontextusban az alkalmazás eléréséhez, hozzá kell adnia a `Calendars.Read` jogosultságot a az alkalmazás regisztrációs adatait, majd adja hozzá a `Calendars.Read` a hatókör a `acquireTokenSilent` hívható meg. A felhasználó kérheti további hozzájárulásokat azoktól a hatókörök számának növelésével.

<!--end-collapse-->



