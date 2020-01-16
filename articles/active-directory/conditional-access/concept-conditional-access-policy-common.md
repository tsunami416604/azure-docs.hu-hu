---
title: Általános feltételes hozzáférési házirendek – Azure Active Directory
description: Gyakran használt feltételes hozzáférési szabályzatok a szervezeteknél
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5cfe6b790d5eb2877e46845317779c89cf33884
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978022"
---
# <a name="common-conditional-access-policies"></a>Gyakori feltételes hozzáférési szabályzatok

A [biztonsági alapértékek](../fundamentals/concept-fundamentals-security-defaults.md) kiválóan használhatók, de számos szervezetnek nagyobb rugalmasságra van szüksége az általuk kínáltnál. Előfordulhat például, hogy a többtényezős hitelesítést igénylő feltételes hozzáférési szabályzatokból bizonyos fiókokat ki kell zárnia, mint a vészhelyzeti hozzáférés vagy a bomlás-üveg felügyeleti fiókok. Ezekben a szervezeteknél a cikkben hivatkozott általános szabályzatok is használhatók.

![Feltételes hozzáférési szabályzatok a Azure Portal](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>Vészhelyzeti hozzáférési fiókok

További információ a segélyhívó fiókokról és azok fontos okairól a következő cikkekben talál további információt: 

* [Vészhelyzeti hozzáférési fiókok kezelése az Azure AD-ben](../users-groups-roles/directory-emergency-access.md)
* [Rugalmas hozzáférés-vezérlési felügyeleti stratégia létrehozása Azure Active Directory](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>A szervezetek által központilag telepített általános házirendek

* [MFA megkövetelése rendszergazdák számára](howto-conditional-access-policy-admin-mfa.md)\*
* [MFA megkövetelése az Azure felügyeleti](howto-conditional-access-policy-azure-management.md)\*
* [MFA megkövetelése minden felhasználónál](howto-conditional-access-policy-all-users-mfa.md)\*
* [Örökölt hitelesítési\* tiltása](howto-conditional-access-policy-block-legacy.md)
* [Kockázatalapú feltételes hozzáférés (prémium szintű Azure AD P2 szükséges)](howto-conditional-access-policy-risk.md)
* [Megbízható hely megkövetelése az MFA-regisztrációhoz](howto-conditional-access-policy-registration.md)
* [Hozzáférés letiltása hely szerint](howto-conditional-access-policy-location.md)
* [Megfelelő eszköz megkövetelése](howto-conditional-access-policy-compliant-device.md)

\* a négy házirendet, ha együtt konfigurálták, a [biztonsági alapértékek](../fundamentals/concept-fundamentals-security-defaults.md)által engedélyezett funkciókat utánozza.

## <a name="next-steps"></a>Következő lépések

- [A bejelentkezési viselkedés szimulálása a feltételes hozzáférési What If eszköz használatával.](troubleshoot-conditional-access-what-if.md)
- [A feltételes hozzáféréshez csak jelentési módot használhat az új házirend-döntések hatásának megállapításához.](concept-conditional-access-report-only.md)
