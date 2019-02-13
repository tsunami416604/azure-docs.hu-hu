---
title: Az Azure MFA-kiszolgáló vagy szolgáltatás, a helyszíni vagy a felhőben?
description: Mint Azure AD-rendszergazda, kell megérteni a többtényezős hitelesítés melyik verzióját szeretnék üzembe kell helyeznie?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9fb7785487fc79daca56e35ce0c6a19efd5b0605
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56195753"
---
# <a name="which-version-of-azure-mfa-is-right-for-my-organization"></a>Az Azure MFA melyik verziója a szervezetem számára megfelelő?

Mielőtt eldöntheti, hol és hogyan multi-factor Authentication (MFA) üzembe helyezéséhez három alapvető kérdésre válaszolnia kell.

* [Mit próbálok biztonságossá tenni?](#what-am-i-trying-to-secure)
* [Hol tárolja a rendszer a felhasználókat?](#where-are-the-users-located)
* [Mely szolgáltatásokra van szükségem?](#what-features-do-i-need)

Minden, az alábbi szakaszokban részletesen segítséget nyújtanak az előző kérdésre.

## <a name="what-am-i-trying-to-secure"></a>Mit próbálok biztonságossá tenni?

A megfelelő kétlépéses ellenőrzési megoldás meghatározásához először kérdésre válaszolnia kell az, hogy mit szeretne biztonságossá tenni egy további hitelesítési tényezővel az. Egy alkalmazást az Azure-ban? Vagy egy távelérésű rendszert? Azzal, hogy megállapítjuk, mit szeretne biztonságossá tételéhez, tudja válaszolni, ahol a multi-factor Authentication szolgáltatás engedélyeznie kell a kérdésre.

| Mit próbál biztonságossá tenni? | MFA a felhőben | MFA-kiszolgáló |
| --- |:---:|:---:|
| Belső Microsoft-alkalmazások |● |● |
| SaaS-alkalmazások az alkalmazáskatalógusban |● |  |
| Az Azure AD-alkalmazásproxyn keresztül közzétett webalkalmazások |● |  |
| Nem az Azure AD-alkalmazásproxyn keresztül közzétett IIS-alkalmazások | |● |
| Távelérés, például VPN vagy RDG | ● | ● |

## <a name="where-are-the-users-located"></a>Hol tárolja a rendszer a felhasználókat?

Következő lépésként állapítsa meg, ahol a szervezet felhasználói található segít meghatározni a megfelelő megoldást, a felhőben vagy a helyszínen az MFA-kiszolgáló használatával.

| Felhasználó helye | MFA a felhőben | MFA-kiszolgáló |
| --- |:---:|:---:|
| Azure Active Directory |● | |
| Azure AD és helyszíni AD összevonással az AD FS-sel |● |● |
| Az Azure AD és helyszíni AD-bA az Azure AD Connect - nincs Jelszókivonat-szinkronizálás és átmenő hitelesítés |● |● |
| Az Azure AD és helyszíni AD-bA az Azure AD Connect - jelszó Jelszókivonat szinkronizálása vagy átmenő hitelesítéssel |● | |
| Helyszíni Active Directory | |● |

## <a name="what-features-do-i-need"></a>Mely szolgáltatásokra van szükségem?

A következő táblázat a felhőbeli Multi-Factor Authentication és a Multi-Factor Authentication-kiszolgáló szolgáltatásait hasonlítja össze.

| Szolgáltatás | MFA a felhőben | MFA-kiszolgáló |
| --- |:---:|:---:|
| Mobilalkalmazásos értesítés második tényezőként | ● | ● |
| Mobilalkalmazásos ellenőrzőkód második tényezőként | ● | ● |
| Telefonhívás második tényezőként | ● | ● |
| Egyirányú SMS második tényezőként | ● | ● |
| Hardvertokenek második tényezőként | ● (nyilvános előzetes verzió) | ● |
| Alkalmazásjelszavak az MFA-t nem támogató Office 365-ügyfelekhez | ● | |
| A hitelesítési módszerek rendszergazdai szabályozása | ● | ● |
| PIN-mód | | ● |
| Csalási riasztás | ● | ● |
| MFA-jelentések | ● | ● |
| Egyszeri mellőzés | | ● |
| Egyéni üdvözlések a telefonhívásokhoz | ● | ● |
| Testreszabható hívóazonosító a telefonhívásokhoz | ● | ● |
| Megbízható IP-címek | ● | ● |
| MFA megjegyzése megbízható eszközökön | ● | |
| Feltételes hozzáférés | ● | ● |
| Gyorsítótár |  | ● |

## <a name="next-steps"></a>További lépések

Most, hogy tisztában van a különbséggel a felhőalapú Azure Multi-Factor Authentication és a helyszíni MFA-kiszolgáló között, beállíthatja és használatba veheti az Azure Multi-Factor Authenticationt. **Válassza ki az ikont, amely a forgatókönyvéhez tartozik**

<center>

[![MFA a felhőben](./media/concept-mfa-whichversion/cloud2.png)](howto-mfa-getstarted.md)  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [![MFA-kiszolgáló](./media/concept-mfa-whichversion/server2.png)](howto-mfaserver-deploy.md) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; </center>
