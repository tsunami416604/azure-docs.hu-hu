---
title: 'Azure AD Connect: Átmenő hitelesítés – Jelenlegi korlátozások | Microsoft dokumentumok'
description: Ez a cikk az Azure Active Directory (Azure AD) átadó hitelesítésének jelenlegi korlátait ismerteti
services: active-directory
keywords: Azure AD Connect áthaladási hitelesítés, active directory telepítése, szükséges összetevők az Azure AD, Egyszeri bejelentkezés, Egyszeri bejelentkezés hez
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/04/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97dc67d46b08bf5765c59806b45edd82f38720cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60347741"
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Azure Active Directory átadó hitelesítés: Jelenlegi korlátozások

>[!IMPORTANT]
>Az Azure Active Directory (Azure AD) átmenő hitelesítés egy ingyenes funkció, és nem kell semmilyen fizetős azure AD-kiadások használatához. Az átmenő hitelesítés csak az Azure AD világszintű példányában érhető el, a [Microsoft Azure Germany felhőben](https://www.microsoft.de/cloud-deutschland) vagy a [Microsoft Azure Government felhőben](https://azure.microsoft.com/features/gov/)nem.

## <a name="supported-scenarios"></a>Támogatott esetek

A következő forgatókönyvek támogatottak:

- Felhasználói bejelentkezések webböngésző-alapú alkalmazásokba.
- Az Outlook-ügyfelekbe olyan örökölt protokollok használatával jelentkezik be az Outlook-ügyfelekre, mint az Exchange ActiveSync, az EAS, az SMTP, a POP és az IMAP.
- Felhasználói bejelentkezések a [modern hitelesítést](https://www.microsoft.com/en-us/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview)támogató régebbi Office-ügyfélalkalmazásokba és Office-alkalmazásokba: Office 2013-as és 2016-os verziók.
- Felhasználói bejelentkezések örökölt protokollalkalmazásokba, például a PowerShell 1.0-s verziójába és másokba.
- Az Azure AD csatlakozik a Windows 10-es eszközökhöz.
- Alkalmazásjelszavak a többtényezős hitelesítéshez.

## <a name="unsupported-scenarios"></a>Nem támogatott forgatókönyvek

A következő esetek _nem_ támogatottak:

- A [kiszivárgott hitelesítő adatokkal](../reports-monitoring/concept-risk-events.md#leaked-credentials)rendelkező felhasználók észlelése.
- Az Azure AD tartományi szolgáltatások jelszókivonat-szinkronizálást kell engedélyezni a bérlőn. Ezért az átmenő hitelesítést használó bérlők _csak_ az Azure AD tartományi szolgáltatásokat igénylő forgatókönyvek esetén nem működnek.
- Az átmenő hitelesítés nincs integrálva az [Azure AD Connect Health szolgáltatásba.](whatis-hybrid-identity-health.md)

> [!IMPORTANT]
> Csak a nem támogatott forgatókönyvek kerülő megoldásaként _(az_ Azure AD Connect health integráció kivételével) engedélyezze a jelszókivonat-szinkronizálást az Azure AD Connect varázsló [Választható szolgáltatások](how-to-connect-install-custom.md#optional-features) lapján.
> 
> [!NOTE]
> Jelszókivonat-szinkronizálás engedélyezése lehetővé teszi a feladatátvételi hitelesítést, ha a helyszíni infrastruktúra megszakad. Ez a feladatátvétel az átmenő hitelesítésés a jelszókivonat-szinkronizálás között nem automatikus. A bejelentkezési módszert manuálisan kell átváltania az Azure AD Connect használatával. Ha az Azure AD Connect szolgáltatást futtató kiszolgáló leáll, a Microsoft támogatási szolgálatától kell segítséget használnia az átmenő hitelesítés kikapcsolásához.

## <a name="next-steps"></a>További lépések
- [Gyors indítás:](how-to-connect-pta-quick-start.md)Az Azure AD áthaladási hitelesítése.
- [Az AD FS-ről átmenő hitelesítésre való áttelepítés](https://aka.ms/ADFSTOPTADPDownload) részletes útmutató az AD FS-ről (vagy más összevonási technológiákról) az átmenő hitelesítésre való áttelepítéshez.
- [Intelligens zárolás:](../authentication/howto-password-smart-lockout.md)Ismerje meg, hogyan konfigurálhatja a bérlő intelligens zárolási képességét a felhasználói fiókok védelme érdekében.
- [Technikai mélymerülés:](how-to-connect-pta-how-it-works.md)Ismerje meg, hogyan működik az átmenő hitelesítés funkció.
- [Gyakori kérdések](how-to-connect-pta-faq.md): Válaszok az átmenő hitelesítés funkcióval kapcsolatos gyakori kérdésekre.
- [Hibaelhárítás:](tshoot-connect-pass-through-authentication.md)Ismerje meg, hogyan oldhatja meg az átmenő hitelesítés szolgáltatással kapcsolatos gyakori problémákat.
- [Biztonsági mélymerülés:](how-to-connect-pta-security-deep-dive.md)Részletes technikai információk beszerezhető az átmenő hitelesítés funkcióról.
- [Azure AD seamless egyszeri bejelentkezés:](how-to-connect-sso.md)További információ erről a kiegészítő funkcióról.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Az Azure Active Directory fórum használatával új szolgáltatáskérelmek et nyújthat be.
