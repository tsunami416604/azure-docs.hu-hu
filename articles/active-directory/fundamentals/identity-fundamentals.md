---
title: Mik az Azure identitáskezelési és hozzáférés-kezelés alapjait? – Az azure Active Directory |} A Microsoft Docs
description: Ismerje meg a speciális védelmi funkciókat és további eszközök Azure Active Directory Premium kiadása elérhető.
services: active-directory
author: eross-msft
manager: mtillman
ms.author: lizross
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/13/2018
ms.reviewer: jsnow
ms.custom: it-pro
ms.openlocfilehash: f7baa29c77ae4af9813bfc755a39cc07288a3ad2
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45734675"
---
# <a name="what-are-the-fundamentals-of-azure-identity-and-access-management"></a>Mik az Azure identitáskezelési és hozzáférés-kezelés alapjait?
Prémium szintű Azure AD felhőalapú identitás- és hozzáférés felügyeleti megoldás is, a speciális védelmi funkciókat. Speciális képességek segítségével biztonságos identitást biztosíthat összes alkalmazását, az identity protection (a továbbfejlesztett a [Microsoft intelligens biztonsági graph](https://www.microsoft.com/security/intelligence)), és [Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md). Az Azure AD segít a felhasználók identitását a valós idejű, így hozhat létre a kockázatalapú és adaptív hozzáférési szabályzatok a munkahelyi adatok védelme érdekében.

Az alábbi rövid videó az Azure AD identitáskezelési és -védelmi funkcióiról nyújt gyors áttekintést:
>[!VIDEO https://www.youtube.com/embed/9LGIJ2-FKIM]

Az Azure AD is biztosít eszközöket, amelyek segítségével biztonságos, automatizálhatja és felügyelheti a környezetben, beleértve a jelszavakat, a felhasználók és csoportok kezelése és az alkalmazás kérelmek alaphelyzetbe állítása. Az Azure AD segítségével kezelheti a felhasználói tulajdonú eszközök és a hozzáférés és -felügyelet szoftver (SaaS) Szolgáltatottszoftver-alkalmazásokat.

Az Azure Active Directory Premium-kiadások és a kapcsolódó eszközöket a költségekkel kapcsolatos további információkért lásd: [Azure Active Directory díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="connect-on-premises-active-directory-with-azure-ad-and-office-365"></a>A helyszíni Active Directory csatlakoztatása az Azure AD-hez és az Office 365-höz
A helyszíni Active Directory megvalósítása a felhőbe kiterjeszti a helyszíni címtárak integrálása az Azure AD-jével [hibrid Identitáskezelés](https://aka.ms/aadframework). [Az Azure AD Connect](../connect/active-directory-aadconnect.md) biztosít ezt az integrációt, így a felhasználók egyetlen identitás- és egyszeri bejelentkezés (SSO) hozzáférést a helyszíni erőforrások és a felhőszolgáltatások, például az Office 365.

Az Azure AD Connect identitásintegrációs eszközök, például a DirSync és az Azure AD Sync és útmutatás nyújtása a helyszíni és az Azure AD közötti szinkronizálás identitás igényeinek támogatásához régebbi verzióit váltja fel. Az Azure AD Connect-szinkronizálás keresztül engedélyezhető:

- **A szinkronizálás.** Felelős a felhasználók, csoportok és más objektumok létrehozásához. A rendszer is meggyőződhet, hogy a helyszíni felhasználók számára a azonosító adatok megegyezik, mi az Azure AD-ben. Ne tudják bekapcsolni a jelszóvisszaírást emellett elősegíti a helyszíni címtárak szinkronizálása Ha a felhasználói jelszavak az Azure ad-ben.

- A hitelesítés. A megfelelő hitelesítési módszer választása akkor fontos, ha az Azure AD hibrid identitáskezelési megoldás beállítása. Kiválaszthatja a felhőalapú hitelesítés (Jelszókivonat-szinkronizálás és átmenő hitelesítés) vagy összevont hitelesítés (AD FS) a szervezet számára. A rendelkezésre álló beállításokkal kapcsolatos további információkért lásd: [válassza ki a megfelelő hitelesítési módszert az Azure Active Directory hibrid identitáskezelési megoldás](https://aka.ms/auth-options).

- **Szolgáltatásállapot-figyelést.** Az Azure AD Connect Health figyelését teszi lehetővé, és egy központi helyen, az Azure Portalon, az ilyen tevékenységek megtekintéséhez. További információkért lásd: [A helyszíni identitás-infrastruktúra és a szinkronizálási szolgáltatások megfigyelése a felhőben](../connect-health/active-directory-aadconnect-health.md).

## <a name="increase-productivity-and-reduce-helpdesk-costs-with-self-service-and-single-sign-on-experiences"></a>A hatékonyság növelése és az ügyfélszolgálati költségek csökkentése önkiszolgáló műveletekkel és egyszeri bejelentkezéssel
A felhasználók menthetnek egyetlen felhasználónév és jelszó, valamint minden eszközön egységes élményt eltöltött idő. Felhasználók is időt takaríthat meg az önkiszolgáló feladatok, például végrehajtása[elfelejtett jelszó alaphelyzetbe állítása](../user-help/active-directory-passwords-update-your-own-password.md) vagy alkalmazásokhoz való hozzáférés kérése nélkül a segélyszolgálat segítségét.

Az egyszeri Bejelentkezéssel és egységes felhasználói élményt, az Azure AD vevőknek [kiterjeszti a helyszíni Active Directory](../connect/active-directory-aadconnect.md) a-felhőbe, hogy a tartományhoz csatlakoztatott eszközökhöz, azok elsődleges munkahelyi fiókok használhatók a felhasználók vállalati erőforrásokhoz, és a webes és a SaaS-alkalmazásokhoz szükségük van egy feladat végrehajtása le. 

Ezenkívül alkalmazás-hozzáférés is automatikusan üzembe helyezett (vagy Szolgáltatáskulcs) a csoporttagságokat és a egy felhasználó alkalmazott állapotát, így alapján katalógus vagy a saját helyszíni alkalmazásokat, amelyek kifejlesztett és az keresztül közzétett az adathozzáférés szabályozása a [Azure AD-alkalmazásproxy](../manage-apps/application-proxy.md).

## <a name="manage-and-control-access-to-your-organizational-resources"></a>Kezelheti, és a szervezeti erőforrásokhoz való hozzáférés szabályozása
A Microsoft identitás- és hozzáférés-felügyeleti megoldások segítségével az alkalmazásokhoz és erőforrásokhoz való hozzáférésének védelméhez, a szervezet adatközpontok és a felhőben. A hozzáférés-kezelés segítségével adja meg például érvényesítési szintű [multi-factor Authentication](../authentication/concept-mfa-howitworks.md) és [feltételes hozzáférési szabályzatok](../conditional-access/overview.md). Gyanús tevékenység keresztül figyelése Speciális biztonsági jelentések, naplózás, és riasztási is segítenek, csökkentheti a potenciális biztonsági problémákat.

A prémium szintű Azure AD feltételes hozzáférési szabályzatok segítségével teszi lehetővé a házirend-alapú hozzáférési szabályok létrehozását az minden olyan Azure AD-hez csatlakozó alkalmazást, például az SaaS-alkalmazások, a felhőben vagy a helyszíni vagy a web Apps alkalmazások futó egyéni alkalmazások). Az Azure AD kiértékeli a valós idejű, a szabályok érvényesítésük, amikor egy felhasználó megpróbál hozzáférni egy alkalmazáshoz. Azure-identitás alkalmazásvédelmi szabályzatokkal automatikusan végezze el a művelet (blokkolja a hozzáférést, vagy többtényezős hitelesítés kényszerítése a felhasználói jelszó alaphelyzetbe állítása) alapján, ha gyanús tevékenységet fel van derítve.

## <a name="azure-active-directory-privileged-identity-management"></a>Azure Active Directory Privileged Identity Management
[Privileged Identity Management (PIM)](../privileged-identity-management/pim-getting-started.md), az Azure Active Directory Premium 2 Edition rendszerhez, segít a felderítése, korlátozását és rendszergazdai fiókok és azok az Azure Active Directory és más erőforrásokhoz való hozzáférésének figyelése A Microsoft online services. A PIM is segítséget nyújt a pontos ideig van szüksége, ami azt jelenti, hogy engedélyezi a rendszergazdák számára, hogy kérése a multi-factor Authentication hitelesítés ideiglenes jogok kiterjesztése az előre konfigurált időn igény szerinti rendszergazdai hozzáférés felügyelete Mielőtt egy normál felhasználói állapotának visszaállításához a fiókjaikat.

## <a name="next-steps"></a>További lépések
Az Azure AD architektúrájával kapcsolatos további információkért lásd: [Mi az az Azure AD architektúra?](active-directory-architecture.md).
