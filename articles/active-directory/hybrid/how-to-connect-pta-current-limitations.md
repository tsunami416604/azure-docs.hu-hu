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
ms.date: 09/04/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: ae6964b36f837931f2278ceef0b330f9badb1d71
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51282713"
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Az Azure Active Directory átmenő hitelesítés: Aktuális korlátozások

>[!IMPORTANT]
>Az Azure Active Directory (Azure AD) átmenő hitelesítése egy ingyenes szolgáltatás, és nem kell minden fizetős kiadásban az Azure AD használatát. Az átmenő hitelesítés csak érhető el a világszerte példány az Azure ad-ben, és nem a a [Microsoft Azure Germany cloud](http://www.microsoft.de/cloud-deutschland) vagy a [Microsoft Azure Government felhőben](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Támogatott esetek

A következő forgatókönyvek támogatottak:

- Felhasználói bejelentkezések böngészőalapú webalkalmazásokhoz.
- Felhasználói bejelentkezések az Outlook-felhasználók számára, például az Exchange ActiveSync, EAS, SMTP, POP és IMAP örökölt protokollok használatával.
- Felhasználói bejelentkezések a régebbi Office-ügyfélalkalmazások és támogató Office-alkalmazások [modern hitelesítést](https://aka.ms/modernauthga): Office 2010, 2013 és 2016-verziók.
- Felhasználói bejelentkezések örökölt protokoll alkalmazások, például a PowerShell 1.0-s verzióját.
- Az Azure AD csatlakoztatja a Windows 10 rendszerű eszközökhöz.
- A multi-factor Authentication alkalmazásjelszókat.

## <a name="unsupported-scenarios"></a>Nem támogatott helyzetek

A következő forgatókönyvek _nem_ támogatja:

- A felhasználók észlelése [kiszivárgott hitelesítő adatok](../reports-monitoring/concept-risk-events.md#leaked-credentials).
- Az Azure AD Domain Services Jelszókivonat-szinkronizálást engedélyezni kell a bérlő van szüksége. Ezért az átmenő hitelesítést használó bérlők _csak_ Azure AD tartományi szolgáltatásokat igénylő forgatókönyvek nem működnek.
- Az átmenő hitelesítés nincs integrálva az [az Azure AD Connect Health](whatis-hybrid-identity-health.md).

>[!IMPORTANT]
>Nem támogatott forgatókönyvek esetében _csak_ (kivéve az Azure AD Connect Health-integráció), engedélyezze a Jelszókivonat-szinkronizálást a [választható funkciók](how-to-connect-install-custom.md#optional-features) lévő Azure AD Connect varázsló.

>[!NOTE]
Jelszókivonat-szinkronizálás engedélyezése teszi meg a feladatátvételi hitelesítési Ha megszakad a helyi infrastruktúrát. A feladatátvétel az átmenő hitelesítés a Jelszókivonat-szinkronizálás nem történik meg automatikusan. Váltson a bejelentkezési módszer, az Azure AD Connect segítségével manuálisan kell. Az Azure AD Connectet futtató kiszolgáló leáll, ha szüksége lesz a segítséget a Microsoft Support átmenő hitelesítés kikapcsolása.

## <a name="next-steps"></a>További lépések
- [Gyors üzembe helyezési](how-to-connect-pta-quick-start.md): első lépésekhez az Azure AD átmenő hitelesítés.
- [Az AD FS át az átmenő hitelesítés](https://aka.ms/ADFSTOPTADPDownload) – egy részletes útmutató, amellyel áttelepíteni az átmenő hitelesítés az Active Directory összevonási szolgáltatások (vagy más összevonási technológiákkal).
- [Az intelligens zárolási](../authentication/howto-password-smart-lockout.md): ismerje meg, hogyan konfigurálhatja az intelligens zárolás funkciót a bérlő felhasználói fiókok védelmét.
- [Részletes technikai](how-to-connect-pta-how-it-works.md): az átmenő hitelesítési szolgáltatás működésének megismerése.
- [Gyakori kérdések](how-to-connect-pta-faq.md): az átmenő hitelesítés szolgáltatással kapcsolatos gyakori kérdésekre adott válaszok.
- [Hibaelhárítás](tshoot-connect-pass-through-authentication.md): ismerje meg az átmenő hitelesítés szolgáltatás szolgáltatással kapcsolatos gyakori problémák megoldásához.
- [A biztonság részletes bemutatása](how-to-connect-pta-security-deep-dive.md): ismerje meg az átmenő hitelesítés szolgáltatás technikai információit.
- [Az Azure AD közvetlen egyszeri bejelentkezés](how-to-connect-sso.md): További információ a kiegészítő funkció.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): az Azure Active Directory-fórumon használatával új funkcióra vonatkozó javaslata fájlt.
