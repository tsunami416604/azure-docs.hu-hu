---
title: Azure többtényezős hitelesítés a szervezet számára – Azure Active Directory
description: Ismerje meg az Azure többtényezős hitelesítésének elérhető funkcióit a szervezet számára a licencmodell alapján
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: febdb708c637ac322c0ca884eae627da9bd5904c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530393"
---
# <a name="overview-of-azure-multi-factor-authentication-for-your-organization"></a>Az Azure többtényezős hitelesítésének áttekintése a szervezetszámára

Az Azure Active Directory (AD) felhasználói számára többféleképpen engedélyezheti az Azure többtényezős hitelesítést a szervezet tulajdonában lévő licencek alapján. 

![Vizsgálja meg a jeleket, és szükség esetén érvényesítse az MFA-t](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

Kutatásaink alapján fiókja több mint 99,9%-kal kisebb valószínűséggel kerül veszélybe, ha többtényezős hitelesítést (MFA) használ.

Tehát hogyan működik a szervezet bekapcsolja mfa még ingyen, mielőtt egy statisztika?

## <a name="free-option"></a>Ingyenes opció

Az Azure AD ingyenes előnyeit használó ügyfelek [a biztonsági alapbeállításokkal engedélyezhetik](../fundamentals/concept-fundamentals-security-defaults.md) a többtényezős hitelesítést a környezetükben.

## <a name="office-365-business-premium-e3-or-e5"></a>Office 365 Vállalati prémium verzió, E3 vagy E5

Az Office 365-tel rendelkező ügyfelek számára két lehetőség van:

* Az Azure többtényezős hitelesítés e vagy engedélyezett, vagy le van tiltva az összes felhasználó, az összes bejelentkezési események. A felhasználók egy részhalmaza esetén, vagy csak bizonyos esetekben nem engedélyezhető többtényezős hitelesítés. A kezelés az Office 365 portálon keresztül történik. 
* A továbbfejlesztett felhasználói élmény érdekében frissítsen az Azure AD Premium P1 vagy P2 szolgáltatásra, és használja a feltételes hozzáférést. További információt az Office 365-erőforrások biztonságossá tétele többtényezős hitelesítéssel című témakörben talál.

## <a name="azure-ad-premium-p1"></a>Prémium szintű Azure AD P1

Az Azure AD Premium P1 vagy hasonló licenccel rendelkező ügyfelek számára, amelyek tartalmazzák ezt a funkciót, például az Enterprise Mobility + Security E3, a Microsoft 365 F1 vagy a Microsoft 365 E3: 

Az [Azure AD feltételes hozzáférés](../conditional-access/overview.md) használatával kérheti a felhasználókat a többtényezős hitelesítésbizonyos forgatókönyvek vagy események során, hogy megfeleljen az üzleti követelményeknek.

## <a name="azure-ad-premium-p2"></a>Prémium szintű Azure AD P2

Az Azure AD Premium P2 vagy hasonló licenccel rendelkező ügyfelek számára, amelyek tartalmazzák ezt a funkciót, például az Enterprise Mobility + Security E5 vagy a Microsoft 365 E5: 

A legerősebb biztonsági pozíciót és jobb felhasználói élményt nyújtja. [Kockázatalapú feltételes hozzáférést ad](../conditional-access/howto-conditional-access-policy-risk.md) hozzá az Azure AD Premium P1 funkciókhoz, amelyek alkalmazkodnak a felhasználói mintákhoz, és minimálisra csökkentik a többtényezős hitelesítési utasításokat.

## <a name="authentication-methods"></a>Hitelesítési módszerek

|   | Alapértelmezett biztonsági szabályok | Minden más módszer |
| --- | --- | --- |
| Értesítés mobilalkalmazáson keresztül | X | X |
| Ellenőrző kód mobilalkalmazásból vagy hardvertokenből |   | X |
| Sms-ben telefonon |   | X |
| Telefonhívás |   | X |

## <a name="next-steps"></a>További lépések

Első lépésekhez tekintse meg a [felhasználói bejelentkezési események azure-beli többtényezős hitelesítéssel való védelméről](../authentication/tutorial-enable-azure-mfa.md)szóló oktatóanyagot.

A licenceléssel kapcsolatos további információkért olvassa el [az Azure többtényezős hitelesítéshez szükséges szolgáltatások és licencek című témakört.](../authentication/concept-mfa-licensing.md)
