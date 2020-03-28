---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: f121be4ec8c3d3ab618e2955d9dbd8ab5eea461d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "79129530"
---
## <a name="test-your-code"></a>A kód tesztelése

A projekt futtatásához a Visual Studióban válassza az **F5**lehetőséget. Az alkalmazás **MainWindow** jelenik meg, ahogy itt látható:

![Az alkalmazás tesztelése](./media/active-directory-develop-guidedsetup-windesktop-test/samplescreenshot.png)

Amikor először futtatja az alkalmazást, és kiválasztja a **Microsoft Graph API hívása** gombot, a rendszer kéri a bejelentkezést. Azure Active Directory-fiók (munkahelyi vagy iskolai fiók) vagy Microsoft-fiók (live.com, outlook.com) használatával tesztelhében.

![Bejelentkezés az alkalmazásba](./media/active-directory-develop-guidedsetup-windesktop-test/signinscreenshot.png)

### <a name="provide-consent-for-application-access"></a>Hozzájárulás hozzájárulása az alkalmazáshoz való hozzáféréshez

Amikor először jelentkezik be az alkalmazásba, a rendszer arra is kéri, hogy adja meg a beleegyezését ahhoz, hogy az alkalmazás hozzáférhessen a profiljához, és bejelentkezhessen Önbe, ahogy az itt látható:

![Adja meg hozzájárulását az alkalmazáshoz való hozzáféréshez](./media/active-directory-develop-guidedsetup-windesktop-test/consentscreen.png)

### <a name="view-application-results"></a>Alkalmazáseredmények megtekintése

A bejelentkezés után meg kell jelennie a Microsoft Graph API-hoz való hívás által visszaadott felhasználói profiladatoknak. Az eredmények az **API-hívás eredménye** mezőben jelennek meg. Alapvető információkat a jogkivonat, amely a `AcquireTokenInteractive` `AcquireTokenSilent` hívás on keresztül szerzett, vagy láthatónak kell lennie a **Token Info** mezőben. Az eredmények a következő tulajdonságokat tartalmazzák:

|Tulajdonság  |Formátum  |Leírás |
|---------|---------|---------|
|**Felhasználónév** |<span>user@domain.com</span> |A felhasználó azonosítására használt felhasználónév.|
|**A token lejár** |DateTime |A jogkivonat lejárati időpontja. Az MSAL szükség esetén meghosszabbítja a lejárati dátumot a jogkivonat megújításával.|


<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>További információ a hatókörökről és a delegált engedélyekről

A Microsoft Graph API-nak a *user.read* hatókörre van szüksége a felhasználói profil olvasásához. Ez a hatókör alapértelmezés szerint automatikusan hozzáadódik az alkalmazásregisztrációs portálon regisztrált minden alkalmazáshoz. A Microsoft Graph egyéb API-k, valamint a háttérkiszolgáló egyéni API-jai további hatóköröket igényelhetnek. A Microsoft Graph API-nak a *Naptárak.Read* hatókörszükséges a felhasználó naptárai listázásához.

Ha egy alkalmazás környezetében szeretné elérni a felhasználó naptárait, adja hozzá a *Calendars.Read* delegált engedélyt az alkalmazás regisztrációs adataihoz. Ezután adja hozzá a *Calendars.Read* hatókört a `acquireTokenSilent` híváshoz.

>[!NOTE]
>A rendszer további jóváhagyásokat kérhet a felhasználótól, ahogy növeli a hatókörök számát.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
