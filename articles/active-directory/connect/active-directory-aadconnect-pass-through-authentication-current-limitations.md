---
title: 'Az Azure AD Connect: Az átmenő hitelesítés – aktuális korlátozások |} A Microsoft Docs'
description: Ez a cikk ismerteti a jelenlegi korlátozások az Azure Active Directory (Azure AD) átmenő hitelesítése
services: active-directory
keywords: Az Azure AD Connect az átmenő hitelesítés, Active Directory telepítése szükséges összetevők SSO, Azure AD egyszeri bejelentkezés
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 34b83c54e31ed73af3f776a6add8f218dda35cf7
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2018
ms.locfileid: "37918920"
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Az Azure Active Directory átmenő hitelesítés: Aktuális korlátozások

>[!IMPORTANT]
>Az Azure Active Directory (Azure AD) átmenő hitelesítése egy ingyenes szolgáltatás, és nem kell minden fizetős kiadásban az Azure AD használatát. Az átmenő hitelesítés csak érhető el a világszerte példány az Azure ad-ben, és nem a a [Microsoft Azure Germany cloud](http://www.microsoft.de/cloud-deutschland) vagy a [Microsoft Azure Government felhőben](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Támogatott esetek

Teljes mértékben támogatottak a következő esetekben:

- Felhasználói bejelentkezések összes böngészőalapú webalkalmazáshoz.
- Felhasználói bejelentkezések a támogató Office-alkalmazások [modern hitelesítést](https://aka.ms/modernauthga): Office 2016 és Office 2013 _a_ modern hitelesítést.
- Felhasználói bejelentkezések az Outlook-felhasználók számára, például az Exchange ActiveSync-, SMTP-, POP és IMAP örökölt protokollok használatával.
- Felhasználói bejelentkezések a Skype for Business, amely támogatja a modern hitelesítést, beleértve az online és a hibrid topológiák. További tudnivalók a támogatott topológiák [Itt](https://technet.microsoft.com/library/mt803262.aspx).
- Az Azure AD domain csatlakoztatja a Windows 10 rendszerű eszközökhöz.
- A multi-factor Authentication alkalmazásjelszókat.

## <a name="unsupported-scenarios"></a>Nem támogatott helyzetek

A következő forgatókönyvek _nem_ támogatja:

- Felhasználói bejelentkezések a az örökölt Office ügyfélalkalmazások számára, az Outlook kivételével (lásd: **támogatott forgatókönyvek** fent): az Office 2010 és Office 2013 _nélkül_ modern hitelesítést. Szervezetek számára javasolt, hogy váltson át a modern hitelesítést, ha lehetséges. Modern hitelesítés lehetővé teszi az átmenő hitelesítés támogatását. Emellett segítséget nyújt a felhasználói fiókokhoz gondoskodhat [feltételes hozzáférési](../active-directory-conditional-access-azure-portal.md) funkciók, például az Azure multi-factor Authentication szolgáltatás.
- A hozzáférést az naptár megosztása és a rendelkezésre állási információk az Exchange hibrid környezetek az Office 2010-et csak.
- Felhasználói bejelentkezések a Skype for Business ügyfélalkalmazások _nélkül_ modern hitelesítést.
- Felhasználói bejelentkezések a PowerShell 1.0-s verzióját. Azt javasoljuk, hogy a PowerShell 2.0-s verzióját használja.
- A felhasználók észlelése [kiszivárgott hitelesítő adatok](../active-directory-reporting-risk-events.md#leaked-credentials).
- Az Azure AD Domain Services Jelszókivonat-szinkronizálást engedélyezni kell a bérlő van szüksége. Ezért az átmenő hitelesítést használó bérlők _csak_ Azure AD tartományi szolgáltatásokat igénylő forgatókönyvek nem működnek.
- Az átmenő hitelesítés nincs integrálva az [az Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md).
- Az Apple Készülékregisztrációs Program (Apple DEP) segítségével az iOS beállítási asszisztens nem támogatja a modern hitelesítést. Ez az Apple DEP-eszközök regisztrálása az Intune-ban a felügyelt tartományokat használ az átmenő hitelesítés sikertelen lesz. Fontolja meg a [céges portál alkalmazás](https://blogs.technet.microsoft.com/intunesupport/2018/02/08/support-for-multi-token-dep-and-authentication-with-company-portal/) alternatívájaként.

>[!IMPORTANT]
>Nem támogatott forgatókönyvek esetében _csak_, a Jelszókivonat-szinkronizálás engedélyezése a [választható funkciók](active-directory-aadconnect-get-started-custom.md#optional-features) lévő Azure AD Connect varázsló. Alkalmazások felhasználói bejelentkezés szerepel a listában szerepel a "nem támogatott forgatókönyvek" szakaszt, ha ezeket megadott bejelentkezési kérelmek vannak _nem_ átmenő hitelesítési ügynökök kezeli, és ezért nem rögzíti a [ Az átmenő hitelesítés naplók](active-directory-aadconnect-troubleshoot-pass-through-authentication.md#collecting-pass-through-authentication-agent-logs).

>[!NOTE]
Jelszókivonat-szinkronizálás engedélyezése teszi meg a feladatátvételi hitelesítési Ha megszakad a helyi infrastruktúrát. Jelszókivonat-szinkronizálást az Active Directory átmenő hitelesítés a feladatátvétel nem automatikus. Váltson a bejelentkezési módszer, az Azure AD Connect segítségével manuálisan kell. Az Azure AD Connectet futtató kiszolgáló leáll, ha szüksége lesz a segítséget a Microsoft Support átmenő hitelesítés kikapcsolása.

## <a name="next-steps"></a>További lépések
- [Gyors üzembe helyezési](active-directory-aadconnect-pass-through-authentication-quick-start.md): első lépésekhez az Azure AD átmenő hitelesítés.
- [Az intelligens zárolási](../authentication/howto-password-smart-lockout.md): ismerje meg, hogyan konfigurálhatja az intelligens zárolás funkciót a bérlő felhasználói fiókok védelmét.
- [Részletes technikai](active-directory-aadconnect-pass-through-authentication-how-it-works.md): az átmenő hitelesítési szolgáltatás működésének megismerése.
- [Gyakori kérdések](active-directory-aadconnect-pass-through-authentication-faq.md): az átmenő hitelesítés szolgáltatással kapcsolatos gyakori kérdésekre adott válaszok.
- [Hibaelhárítás](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): ismerje meg az átmenő hitelesítés szolgáltatás szolgáltatással kapcsolatos gyakori problémák megoldásához.
- [A biztonság részletes bemutatása](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): ismerje meg az átmenő hitelesítés szolgáltatás technikai információit.
- [Az Azure AD közvetlen egyszeri bejelentkezés](active-directory-aadconnect-sso.md): További információ a kiegészítő funkció.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): az Azure Active Directory-fórumon használatával új funkcióra vonatkozó javaslata fájlt.
