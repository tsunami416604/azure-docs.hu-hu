---
title: Gyakori feltételes hozzáférési szabályzatok – Azure Active Directory
description: Általánosan használt feltételes hozzáférési házirendek szervezetek számára
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20b699f0672b49dd2f947e0cf00d0ffcef7961e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295304"
---
# <a name="common-conditional-access-policies"></a>Gyakori feltételes hozzáférési szabályzatok

[A biztonsági alapértékek](../fundamentals/concept-fundamentals-security-defaults.md) néhány számára nagyszerűek, de sok szervezetnek nagyobb rugalmasságra van szüksége, mint amennyit kínálnak. Például sok kell a képesség, hogy kizárják az adott fiókok, például a sürgősségi hozzáférés vagy a törésüveg felügyeleti fiókok feltételes hozzáférés házirendek igénylő többtényezős hitelesítést. Ezek a szervezetek esetében a cikkben hivatkozott közös házirendek használhatók lehetnek.

![Feltételes hozzáférési szabályzatok az Azure Portalon](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>Vészelérési fiókok

További információ a vészelérési fiókokról és azok fontos tudnivalóiról az alábbi cikkekben található: 

* [Vészelérési fiókok kezelése az Azure AD-ben](../users-groups-roles/directory-emergency-access.md)
* [Rugalmas hozzáférés-vezérlési stratégia létrehozása az Azure Active Directoryval](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>Szervezetek által telepített tipikus házirendek

* [Örökölt hitelesítés blokkolása](howto-conditional-access-policy-block-legacy.md)\*
* [Többfa megkövetelése rendszergazdáknak](howto-conditional-access-policy-admin-mfa.md)\*
* [Többéves kor–szolgáltatás megkövetelése az Azure-felügyelethez](howto-conditional-access-policy-azure-management.md)\*
* [Többkori faszükséglés megkövetelése az összes felhasználó számára](howto-conditional-access-policy-all-users-mfa.md)\*

\*Ez a négy házirend, ha együtt van konfigurálva, a [biztonsági alapértelmezések](../fundamentals/concept-fundamentals-security-defaults.md)által engedélyezett funkciókat utánozza.

## <a name="additional-policies"></a>További irányelvek

* [Kockázatalapú feltételes hozzáférés (prémium szintű Azure AD P2 szükséges)](howto-conditional-access-policy-risk.md)
* [Megbízható helyek megkövetelése az MFA-regisztrációhoz](howto-conditional-access-policy-registration.md)
* [Hozzáférés letiltása hely alapján](howto-conditional-access-policy-location.md)
* [Megfelelő eszköz megkövetelése](howto-conditional-access-policy-compliant-device.md)
* [Hozzáférés letiltása adott alkalmazások kivételével](howto-conditional-access-policy-block-access.md)

## <a name="next-steps"></a>További lépések

- [A bejelentkezési viselkedés szimulálása a Feltételes hozzáférés Mi ha eszközzel.](troubleshoot-conditional-access-what-if.md)

- [Az új házirend-döntések hatásának meghatározásához használja a csak jelentésmódot a feltételes hozzáféréshez.](concept-conditional-access-report-only.md)
