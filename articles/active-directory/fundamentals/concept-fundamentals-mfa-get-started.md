---
title: Multi-Factor Authentication engedélyezése a szervezet számára – Azure Active Directory
description: Az Azure MFA engedélyezése a szervezete licence alapján
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe0d79f228ee2b84c00a35d4836cbda6a4847a42
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932449"
---
# <a name="enable-multi-factor-authentication-for-your-organization"></a>Multi-Factor Authentication engedélyezése a szervezet számára

Több módon is engedélyezheti az Azure Multi-Factor Authentication (MFA) használatát a Azure Active Directory (AD) felhasználók számára a szervezet tulajdonában lévő licencek alapján. 

![A jelek vizsgálata és az MFA betartatása szükség esetén](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

Tanulmányunk alapján a fiókja több mint 99,9%-kal kevésbé valószínű, ha MFA-t használ.

Hogy a szervezet hogyan kapcsolja be a többtényezős hitelesítést még a statisztika létrehozása előtt?

## <a name="free-option"></a>Ingyenes lehetőség

Az Azure AD ingyenes előnyeit kihasználó ügyfelek [biztonsági alapértékekkel](../fundamentals/concept-fundamentals-security-defaults.md) engedélyezhetik a többtényezős hitelesítést a környezetében.

## <a name="office-365"></a>Office 365

Az Office 365-ügyfeleknél két lehetőség közül választhat:

- A [biztonsági alapértékek](concept-fundamentals-security-defaults.md) az Azure ad-n keresztül engedélyezhetők az Azure multi-Factor Authentication összes felhasználójának védelme érdekében.
- Ha a szervezet több részletességet igényel a többtényezős hitelesítés biztosításához, az Office-licencek [FELHASZNÁLÓNKÉNTI MFA-](../authentication/howto-mfa-userstates.md) képességeket is tartalmaznak. Felhasználónkénti MFA engedélyezve és kikényszerítve minden felhasználónál, a rendszergazdák egyénileg.

## <a name="azure-ad-premium-p1"></a>Prémium szintű Azure AD P1

Prémium szintű Azure AD P1 vagy hasonló licenccel rendelkező ügyfelek esetében, például Enterprise Mobility + Security E3, Microsoft 365 F1 vagy Microsoft 365 E3: 

Az ajánlott eljárás a [feltételes hozzáférési szabályzatok](../conditional-access/concept-conditional-access-policy-common.md) használata a legjobb felhasználói élményhez.

## <a name="azure-ad-premium-p2"></a>Prémium szintű Azure AD P2

Prémium szintű Azure AD P2 vagy hasonló licenccel rendelkező ügyfelek számára, mint például a Enterprise Mobility + Security E5 vagy a Microsoft 365 E5: 

A javaslat a [feltételes hozzáférési szabályzatok](../conditional-access/concept-conditional-access-policy-common.md) és az [Identity Protection](../identity-protection/overview-v2.md) kockázati szabályzatok használata a legjobb felhasználói élmény és a kényszerítés rugalmassága érdekében.

## <a name="authentication-methods"></a>Hitelesítési módok

|   | Alapértelmezett biztonsági szabályok | Minden egyéb módszer |
| --- | --- | --- |
| Értesítés mobilalkalmazásban | X | X |
| Mobilalkalmazásbeli ellenőrző kód vagy hardvertoken |   | X |
| Telefonra küldött szöveges üzenet |   | X |
| Telefonos hívás |   | X |
| Alkalmazásjelszók |   | X * * |

\* * Az alkalmazások jelszavai csak akkor érhetők el felhasználónkénti MFA-ban, ha az örökölt hitelesítési forgatókönyvek csak a rendszergazdák által engedélyezettek.

## <a name="next-steps"></a>Következő lépések

[Az Azure AD díjszabási lapja](https://azure.microsoft.com/pricing/details/active-directory/)
