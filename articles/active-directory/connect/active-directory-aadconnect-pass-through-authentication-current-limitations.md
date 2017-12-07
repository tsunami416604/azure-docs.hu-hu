---
title: "Az Azure AD Connect: Áteresztő hitelesítés – aktuális korlátozásai |} Microsoft Docs"
description: "Ez a cikk ismerteti az Azure Active Directory (Azure AD) áteresztő hitelesítés aktuális korlátozásai"
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
ms.date: 12/05/2017
ms.author: billmath
ms.openlocfilehash: a7edfd1939ad45dd3309fe5eaee2afa36086e9eb
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2017
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Az Azure Active Directory átmenő hitelesítést: Aktuális korlátozások

>[!IMPORTANT]
>Az Azure Active Directory (Azure AD) áteresztő hitelesítés lehetővé teszi az ingyenes, és nem kell használni az Azure AD bármely fizetős verziója. Áteresztő hitelesítés csak érhető el a világméretű példányát az Azure AD, és nem a a [Microsoft Azure Németország felhő](http://www.microsoft.de/cloud-deutschland) vagy a [a Microsoft Azure Government felhő](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Támogatott esetek

A következő forgatókönyvek teljes mértékben támogatottak:

- Felhasználói bejelentkezések minden webes webböngésző-alapú alkalmazásokhoz
- Felhasználói bejelentkezések támogató Office 365-ügyfélalkalmazások számára [modern hitelesítést](https://aka.ms/modernauthga)
- Office 2016 és Office 2013 _rendelkező_ modern hitelesítést
- Az Azure AD tartományi csatlakozik a Windows 10 rendszerű eszközökhöz
- Exchange ActiveSync-támogatását.

## <a name="unsupported-scenarios"></a>Nem támogatott forgatókönyvek

A következő forgatókönyvek _nem_ támogatja:

- Felhasználói bejelentkezések Office-ügyfél régebbi alkalmazásokhoz: Office 2010 és Office 2013 _nélkül_ modern hitelesítést. A szervezetek javasolt, hogy váltani a modern hitelesítést, ha lehetséges. A modern hitelesítés lehetővé teszi, hogy átmenő hitelesítés támogatásához. Emellett segítséget nyújt a felhasználói fiókok használatával biztonságos [feltételes hozzáférés](../active-directory-conditional-access-azure-portal.md) szolgáltatásait, például az Azure multi-factor Authentication.
- Felhasználói bejelentkezések Skype üzleti ügyfélalkalmazások, beleértve a Skype vállalati 2016 esetében.
- Felhasználói bejelentkezések a PowerShell 1.0-s verziója. Azt javasoljuk, hogy a PowerShell 2.0-s verzióját használja.
- Az Azure Active Directory tartományi szolgáltatásokban.
- A multi-factor Authentication alkalmazásjelszókat.
- A felhasználók észlelési [hitelesítő adatok szivárgását](../active-directory-reporting-risk-events.md#leaked-credentials).

>[!IMPORTANT]
>Nem támogatott forgatókönyvek megoldás _csak_, engedélyezze a Jelszókivonat-szinkronizálást a a [választható szolgáltatások](active-directory-aadconnect-get-started-custom.md#optional-features) az Azure AD Connect varázsló lapján.

>[!NOTE]
Jelszókivonat-szinkronizálást engedélyezése lehetővé teszi az feladatátvételi hitelesítési lehetőséget, ha a helyszíni infrastruktúra megszakad. Nincs automatikus a feladatátvétel az áteresztő hitelesítés az Active Directory Jelszókivonat-szinkronizálást. Váltás a bejelentkezési módszer segítségével manuálisan az Azure AD Connect lesz szüksége. Ha az Azure AD Connect szolgáltatást futtató kiszolgáló leáll, szüksége lesz súgó a Microsoft Support áteresztő hitelesítés kikapcsolása.

## <a name="next-steps"></a>Következő lépések
- [Gyors üzembe helyezési](active-directory-aadconnect-pass-through-authentication-quick-start.md):, amelyekből megismerheti az Azure AD átmenő hitelesítéssel.
- [Intelligens zárolás](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): megtudhatja, hogyan konfigurálja az intelligens zárolás funkció a bérlő felhasználói fiókok védelme.
- [Műszaki mélyreható](active-directory-aadconnect-pass-through-authentication-how-it-works.md): a csatlakoztatott hitelesítési szolgáltatás működésének megismerése.
- [Gyakori kérdések](active-directory-aadconnect-pass-through-authentication-faq.md): az áteresztő hitelesítés szolgáltatással kapcsolatos gyakori kérdésekre adott válaszok.
- [Hibaelhárítás](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): Útmutató: az áteresztő hitelesítés szolgáltatás kapcsolatos gyakori problémák megoldásához.
- [Biztonsági mélyreható](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): részletes műszaki információért az áteresztő hitelesítés szolgáltatást.
- [Az Azure AD zökkenőmentes SSO](active-directory-aadconnect-sso.md): további információk a kiegészítő funkció.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): az Azure Active Directory fórumán használja a következő fájl új frissítéseiről.

