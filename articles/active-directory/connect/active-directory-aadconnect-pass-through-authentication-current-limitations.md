---
title: "Az Azure AD Connect: Áteresztő hitelesítés – aktuális korlátozásai |} Microsoft Docs"
description: "Ez a cikk ismerteti a jelenlegi korlátozások az Azure Active Directory (Azure AD) áteresztő hitelesítés."
services: active-directory
keywords: "Az Azure AD Connect áteresztő hitelesítés, telepítés Active Directory szükséges összetevőket az Azure AD, SSO, egyszeri bejelentkezést."
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: billmath
ms.openlocfilehash: 4a33df43ca218545d6c684103a64f2cd1460913b
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2017
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Az Azure Active Directory átmenő hitelesítést: Aktuális korlátozások

>[!IMPORTANT]
>Az Azure AD áteresztő hitelesítés lehetővé teszi az ingyenes, és nem kell használni az Azure AD bármely fizetős verziója. Áteresztő hitelesítés csak érhető el a világméretű példányát az Azure AD, és nem a [Microsoft Cloud Németország](http://www.microsoft.de/cloud-deutschland) és [Microsoft Azure Government felhő](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Támogatott esetek

A következő forgatókönyvek teljes mértékben támogatottak:

- Felhasználói bejelentkezések minden böngésző alapú webalkalmazás.
- Felhasználói bejelentkezések be, amely támogatja az Office 365-ügyfélalkalmazások [modern hitelesítést](https://aka.ms/modernauthga) -Office 2016 és Office 2013 _rendelkező_ modern hitelesítést.
- Az Azure AD Join Windows 10 rendszerű eszközökhöz.
- Exchange ActiveSync-támogatását.

## <a name="unsupported-scenarios"></a>Nem támogatott forgatókönyvek

A következő forgatókönyvek _nem_ támogatja:

- Felhasználói bejelentkezések alkalmazásokba örökölt Office ügyfél - Office 2010 és Office 2013 _nélkül_ modern hitelesítést). A szervezetek javasolt, hogy váltani a modern hitelesítést, ha lehetséges. A modern hitelesítés lehetővé teszi az áteresztő hitelesítés támogatásához azonban is segítséget nyújt a felhasználó mindig lássa fiókok [feltételes hozzáférés](../active-directory-conditional-access-azure-portal.md) funkciók, például a többtényezős hitelesítés (MFA).
- Felhasználói bejelentkezések Skype az üzleti ügyfélalkalmazások, beleértve a Skype vállalati 2016 esetében.
- Felhasználói bejelentkezések a PowerShell 1.0-s verziója. Javasoljuk, hogy inkább PowerShell 2.0-s verzió.
- Azure AD tartományi szolgáltatások.
- A multi-factor Authentication alkalmazásjelszókat.
- A felhasználók észlelési [hitelesítő adatok szivárgását](../active-directory-reporting-risk-events.md#leaked-credentials).

>[!IMPORTANT]
>Nem támogatott forgatókönyvek megoldás _csak_, engedélyezze a Jelszókivonat-szinkronizálást a a [választható szolgáltatások](active-directory-aadconnect-get-started-custom.md#optional-features) az Azure AD Connect varázsló lapján. Engedélyezése a Jelszókivonat-szinkronizálást is biztosít feladatátvételi hitelesítési lehetőséget, ha a helyszíni infrastruktúra teljesen megszakad. A feladatátvétel az áteresztő hitelesítés a Jelszókivonat-szinkronizálást nincs automatikus, de való végzett Microsoft Support segítségével.

## <a name="next-steps"></a>Következő lépések
- [**Gyors üzembe helyezési** ](active-directory-aadconnect-pass-through-authentication-quick-start.md) - létrehozásához, és fut az Azure AD áteresztő hitelesítés.
- [**Intelligens zárolás** ](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) -intelligens zárolás konfigurálása képességet a bérlő a felhasználói fiókok védelme.
- [**Műszaki mélyreható** ](active-directory-aadconnect-pass-through-authentication-how-it-works.md) – Ez a funkció működésének megismerése.
- [**Gyakran ismételt kérdések** ](active-directory-aadconnect-pass-through-authentication-faq.md) -gyakran feltett kérdésekre adott válaszokat.
- [**Hibaelhárítás** ](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) -Útmutató: a szolgáltatással kapcsolatos gyakori problémák megoldása.
- [**Biztonsági mélyreható** ](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) -részletes műszaki további információ a szolgáltatásról.
- [**Az Azure AD zökkenőmentes SSO** ](active-directory-aadconnect-sso.md) -további információ a kiegészítő funkció.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – új funkciókérések tárolásához.
