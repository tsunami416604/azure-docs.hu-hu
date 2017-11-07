---
title: "Az Azure AD v2 Android első lépések – tesztelése |} Microsoft Docs"
description: "Android-alkalmazás hogyan szereznie egy hozzáférési jogkivonatot és hívható meg Microsoft Graph API-val vagy a hozzáférési jogkivonatok az Azure Active Directory v2 végpont igénylő API-k"
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
ms.openlocfilehash: b59c44e351c3b3aa74557d0d1625495277b1bc1a
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
## <a name="test-your-code"></a>Tesztelheti a kódját

1. Az eszköz/emulátor telepíteni a kódot.
2. Ha elkészült, teszteléséhez, a Microsoft Azure Active Directory (szervezeti fiók) vagy a Microsoft Account (live.com, outlook.com) fiókkal való bejelentkezéshez használt. 

![A minta képernyőkép](media/active-directory-mobileanddesktopapp-android-test/mainwindow.png)
<br/><br/>
![Bejelentkezés](media/active-directory-mobileanddesktopapp-android-test/usernameandpassword.png)

### <a name="consent"></a>Hozzájárulás
Az első alkalommal a felhasználó bejelentkezik az alkalmazáshoz fog jelenni számukra hasonló hozzájárulási képernyő a alatt, ahol azok kell explicit módon fogadja el: 

![Hozzájárulás](media/active-directory-mobileanddesktopapp-android-test/androidconsent.png)


### <a name="expected-results"></a>Kívánt eredmény elérése érdekében
Az eredmények Microsoft Graph API hívása a "me" kell megtekintéséhez az beszerzése a felhasználói profil – https://graph.microsoft.com/v1.0/me használt végpontnak. Általános Microsoft Graph-végpontok listáját lásd: a [cikk](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries).

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>További információ a hatókörök és delegált jogosultságokkal sikeresen telepítették

A Microsoft Graph API megköveteli a `user.read` hatókörrel, hogy a felhasználói profil olvasása. Minden egyes a regisztrációs portál regisztrált alkalmazás alapértelmezés szerint automatikusan megjelenik az ebben a hatókörben. Néhány más Microsoft Graph API, továbbá egyéni API-k, a háttér-kiszolgáló további hatókörökkel lehet szükség. Például a Microsoft Graph-hatóköre `Calendars.Read` listát a felhasználók naptáraiban szükséges. A felhasználó naptár kontextusban az alkalmazás eléréséhez, hozzá kell adnia a `Calendars.Read` jogosultságot a az alkalmazás regisztrációs adatait, majd adja hozzá a `Calendars.Read` a hatókör a `acquireTokenSilentAsync` hívható meg. A felhasználó kérheti további hozzájárulásokat azoktól a hatókörök számának növelésével.

<!--end-collapse-->

[!INCLUDE  [Help and Support Options](../../../../includes/active-directory-develop-help-support-include.md)]
