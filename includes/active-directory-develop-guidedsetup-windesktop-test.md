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
ms.openlocfilehash: a11b291ab89dc9f8159e00e1f2304706f041068e
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59503002"
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

Miután bejelentkezett, megjelenik a profilok adatainak a Microsoft Graph API-hívás által visszaadott. Az eredmények megjelennek a **API-hívási eredmények** mezőbe. Alapvető információkat szeretne a jogkivonatot, amely a hívást keresztül szerezték be `AcquireTokenInteractive` vagy `AcquireTokenSilent` meg fognak jelenni a **Tártoken adatainak** mezőbe. Az eredmények az alábbi tulajdonságokat tartalmazzák:

|Tulajdonság  |Formátum  |Leírás |
|---------|---------|---------|

|**Felhasználónév**  | <span> user@domain.com </span> |} A felhasználónév, amelyet a felhasználó azonosítására szolgál. |} | **Jogkivonat lejár** |} Dátum és idő |} Az az időpont, amikor a jogkivonat lejár. Az MSAL kiterjeszti a lejárati dátum szükség szerint a token megújítása. |}


<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>További információ a hatókörök és delegált engedélyek

A Microsoft Graph API megköveteli a *user.read* hatókörrel, hogy a felhasználói profil olvasása. Minden alkalmazás, amely regisztrálva van az alkalmazásregisztrációs portálon alapértelmezés szerint automatikusan megjelenik az ebben a hatókörben. Egyéb Microsoft Graph API-k, valamint a egyéni API-k, a háttér-kiszolgáló további hatókörökkel lehet szükség. A Microsoft Graph API megköveteli a *Calendars.Read* hatókörrel, hogy a felhasználók naptáraiban listázása.

A felhasználók naptáraiban keretén belül egy alkalmazás eléréséhez, adja hozzá a *Calendars.Read* delegált engedéllyel az alkalmazás regisztrációs adatok. Adja hozzá a *Calendars.Read* a hatókört a `acquireTokenSilent` hívja.

>[!NOTE]
>A felhasználó további címtárbérlőhöz hatókörök számának növelésével kérheti.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
