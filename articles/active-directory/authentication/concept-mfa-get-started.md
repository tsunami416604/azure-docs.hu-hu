---
title: Multi-Factor Authentication engedélyezése a szervezet számára – Azure Active Directory
description: Az Azure MFA engedélyezése a szervezete licence alapján
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98bf62dff3e6c642f5de775f2d5bf8682ecea169
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73164925"
---
# <a name="enable-multi-factor-authentication-for-your-organization"></a>Multi-Factor Authentication engedélyezése a szervezet számára

Több módon is engedélyezheti az Azure Multi-Factor Authentication (MFA) használatát a Azure Active Directory (AD) felhasználók számára a szervezet tulajdonában lévő licencek alapján. 

![A jelek vizsgálata és az MFA betartatása szükség esetén](./media/concept-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

Tanulmányunk alapján a fiókja több mint 99,9%-kal kevésbé valószínű, ha MFA-t használ.

Hogy a szervezet hogyan kapcsolja be a többtényezős hitelesítést még a statisztika létrehozása előtt?

## <a name="free-option"></a>Ingyenes lehetőség

Az Azure AD ingyenes előnyeit kihasználó ügyfelek [biztonsági alapértékekkel](../conditional-access/concept-conditional-access-security-defaults.md) engedélyezhetik a többtényezős hitelesítést a környezetében.

## <a name="office-365"></a>Office 365

Az Office 365-ügyfeleknél két lehetőség közül választhat:

- A [biztonsági alapértékek](../conditional-access/concept-conditional-access-security-defaults.md) az Azure ad-n keresztül engedélyezhetők az Azure multi-Factor Authentication összes felhasználójának védelme érdekében.
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
| Értesítés a Mobile App használatával | X | X |
| Ellenőrző kód a Mobile App vagy a Hardware tokenből |   | X |
| SMS-üzenet a telefonra |   | X |
| Telefonos hívás |   | X |
| Alkalmazásjelszók |   | X * * |

\* * Az alkalmazások jelszavai csak akkor érhetők el felhasználónkénti MFA-ban, ha az örökölt hitelesítési forgatókönyvek csak a rendszergazdák által engedélyezettek.

## <a name="next-steps"></a>Következő lépések

[Az Azure AD díjszabási lapja](https://azure.microsoft.com/pricing/details/active-directory/)
