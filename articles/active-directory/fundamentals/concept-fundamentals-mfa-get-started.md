---
title: Azure-Multi-Factor Authentication a szervezet számára – Azure Active Directory
description: Ismerje meg az Azure Multi-Factor Authentication elérhető funkcióit a licencelési modellje alapján a szervezet számára
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
ms.openlocfilehash: 13bb7782f6396466200fbb3e2df77158bb75d8a4
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90705401"
---
# <a name="overview-of-azure-multi-factor-authentication-for-your-organization"></a>A szervezethez tartozó Azure-Multi-Factor Authentication áttekintése

Az Azure Multi-Factor Authentication a szervezet tulajdonában lévő licencek alapján több módon is engedélyezhető az Azure Active Directory (AD) felhasználók számára. 

![A jelek vizsgálata és az MFA betartatása szükség esetén](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

Tanulmányunk alapján a fiókja több mint 99,9%-kal kevésbé valószínű, ha többtényezős hitelesítést (MFA) használ.

Tehát hogyan kapcsolja be a szervezete az MFA-t még ingyenesen a statisztika létrehozása előtt?

## <a name="free-option"></a>Ingyenes lehetőség

Az Azure AD ingyenes előnyeit kihasználó ügyfelek [biztonsági alapértékekkel](../fundamentals/concept-fundamentals-security-defaults.md) engedélyezhetik a többtényezős hitelesítést a környezetében.

## <a name="microsoft-365-business-e3-or-e5"></a>Microsoft 365 Vállalati verzió, E3 vagy E5

A Microsoft 365 rendelkező ügyfeleknek két lehetősége van:

* Az Azure Multi-Factor Authentication az összes felhasználó számára engedélyezve van vagy le van tiltva az összes bejelentkezési esemény esetében. Nincs lehetőség a többtényezős hitelesítés engedélyezésére a felhasználók egy részhalmaza számára, vagy csak bizonyos esetekben. A felügyelet az Office 365-portálon keresztül történik. 
* A jobb felhasználói élmény érdekében frissítsen prémium szintű Azure AD P1-re vagy P2-re, és használja a feltételes hozzáférést. További információ: biztonságos Microsoft 365 erőforrások a többtényezős hitelesítéssel.

## <a name="azure-ad-premium-p1"></a>Prémium szintű Azure AD P1

Prémium szintű Azure AD P1 vagy hasonló licenccel rendelkező ügyfelek esetében, például Enterprise Mobility + Security E3, Microsoft 365 F1 vagy Microsoft 365 E3: 

Az [Azure ad feltételes hozzáférés](../authentication/tutorial-enable-azure-mfa.md) használatával a felhasználók a többtényezős hitelesítésre való rákérdezéshez bizonyos esetekben vagy eseményeknél, az üzleti igényeknek megfelelően.

## <a name="azure-ad-premium-p2"></a>Prémium szintű Azure AD P2

Prémium szintű Azure AD P2 vagy hasonló licenccel rendelkező ügyfelek számára, mint például a Enterprise Mobility + Security E5 vagy a Microsoft 365 E5: 

Biztosítja a legerősebb biztonsági helyzetet és a jobb felhasználói élményt. [Kockázatalapú feltételes hozzáférést biztosít](../conditional-access/howto-conditional-access-policy-risk.md) a prémium szintű Azure ad P1-funkciókhoz, amelyek alkalmazkodnak a felhasználói mintákhoz, és lekicsinyítik a többtényezős hitelesítési kéréseket.

## <a name="authentication-methods"></a>Hitelesítési módszerek

| Metódus | Alapértelmezett biztonsági szabályok | Minden egyéb módszer |
| --- | --- | --- |
| Értesítés a Mobile App használatával | X | X |
| Ellenőrző kód a Mobile App vagy a Hardware tokenből |   | X |
| SMS-üzenet a telefonra |   | X |
| Telefonos hívás |   | X |

## <a name="next-steps"></a>További lépések

Az első lépésekhez tekintse meg az oktatóanyagot a [felhasználói bejelentkezési események biztonságossá tételéhez az Azure multi-Factor Authentication](../authentication/tutorial-enable-azure-mfa.md)használatával.

A licenceléssel kapcsolatos további információkért lásd: [Az Azure multi-Factor Authentication szolgáltatásai és licencei](../authentication/concept-mfa-licensing.md).
