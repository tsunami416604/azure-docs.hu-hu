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
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 6345eca674086801f8bb0f45476009f04a10f2e3
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58214481"
---
## <a name="test-your-code"></a>Tesztelheti a kódját

Futtassa a projektet a Visual Studióban, válassza a **F5**. Az alkalmazás **MainWindow** jelenik meg, ahogy az itt látható:

![Az alkalmazás tesztelése](./media/active-directory-develop-guidedsetup-windesktop-test/samplescreenshot.png)

Az első alkalommal futtatja az alkalmazást, és válassza ki a **Microsoft Graph API meghívása** gomb a kéri, jelentkezzen be. Használja az Azure Active Directory-fiókkal (munkahelyi vagy iskolai fiók) vagy Microsoft-fiókkal (live.com, Outlook.com-os) teszteléséhez.

![Bejelentkezés az alkalmazásba](./media/active-directory-develop-guidedsetup-windesktop-test/signinscreenshot.png)

### <a name="provide-consent-for-application-access"></a>Alkalmazás-hozzáférési hozzájárulás megadása

Először jelentkezik be az alkalmazás is kéri, adja meg, beleegyezik, hogy az alkalmazás érhető el a profil, és itt látható módon a szolgáltatásba:

![Adja meg az Ön hozzájárul az alkalmazás-hozzáférés](./media/active-directory-develop-guidedsetup-windesktop-test/consentscreen.png)

### <a name="view-application-results"></a>Kérelem eredményének megtekintése

Miután bejelentkezett, megjelenik a profilok adatainak a Microsoft Graph API-hívás által visszaadott. Az eredmények megjelennek a **API-hívási eredmények** mezőbe. Alapvető információkat szeretne a jogkivonatot, amely a hívást keresztül szerezték be `AcquireTokenAsync` vagy `AcquireTokenSilentAsync` meg fognak jelenni a **Tártoken adatainak** mezőbe. Az eredmények az alábbi tulajdonságokat tartalmazzák:

|Tulajdonság  |Formátum  |Leírás |
|---------|---------|---------|
|**Name (Név)** |A felhasználó teljes neve |A felhasználó vezetékneve és nevét.|
|**Felhasználónév** |<span>user@domain.com</span> |A felhasználónév, amely a felhasználó azonosítására szolgál.|
|**Jogkivonat lejár** |DateTime |Az az időpont, amikor a jogkivonat lejár. Az MSAL szükség szerint a token megújítása kiterjeszti a lejárati dátumot.|
|**Hozzáférési jogkivonat** |String |A jogkivonat-karakterláncot, amelyet elküld a HTTP-kérelmek, amely esetében egy *engedélyeztetési fejléc*.|

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>További információ a hatókörök és delegált engedélyek

A Microsoft Graph API megköveteli a *user.read* hatókörrel, hogy a felhasználói profil olvasása. Minden alkalmazás, amely regisztrálva van az alkalmazásregisztrációs portálon alapértelmezés szerint automatikusan megjelenik az ebben a hatókörben. Egyéb Microsoft Graph API-k, valamint a egyéni API-k, a háttér-kiszolgáló további hatókörökkel lehet szükség. A Microsoft Graph API megköveteli a *Calendars.Read* hatókörrel, hogy a felhasználók naptáraiban listázása.

A felhasználók naptáraiban keretén belül egy alkalmazás eléréséhez, adja hozzá a *Calendars.Read* delegált engedéllyel az alkalmazás regisztrációs adatok. Adja hozzá a *Calendars.Read* a hatókört a `acquireTokenSilent` hívja.

>[!NOTE]
>A felhasználó további címtárbérlőhöz hatókörök számának növelésével kérheti.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
