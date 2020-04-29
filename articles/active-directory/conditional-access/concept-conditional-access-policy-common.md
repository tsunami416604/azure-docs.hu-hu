---
title: Általános feltételes hozzáférési házirendek – Azure Active Directory
description: Gyakran használt feltételes hozzáférési szabályzatok a szervezeteknél
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80295304"
---
# <a name="common-conditional-access-policies"></a>Gyakori feltételes hozzáférési szabályzatok

A [biztonsági alapértékek](../fundamentals/concept-fundamentals-security-defaults.md) kiválóan használhatók, de számos szervezetnek nagyobb rugalmasságra van szüksége az általuk kínáltnál. Előfordulhat például, hogy a többtényezős hitelesítést igénylő feltételes hozzáférési szabályzatokból bizonyos fiókokat ki kell zárnia, mint a vészhelyzeti hozzáférés vagy a bomlás-üveg felügyeleti fiókok. Ezekben a szervezeteknél a cikkben hivatkozott általános szabályzatok is használhatók.

![Feltételes hozzáférési szabályzatok a Azure Portal](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>Vészhelyzeti hozzáférési fiókok

További információ a segélyhívó fiókokról és azok fontos okairól a következő cikkekben talál további információt: 

* [Vészhelyzeti hozzáférési fiókok kezelése az Azure AD-ben](../users-groups-roles/directory-emergency-access.md)
* [Rugalmas hozzáférés-vezérlési felügyeleti stratégia létrehozása Azure Active Directory](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>A szervezetek által központilag telepített általános házirendek

* [Örökölt hitelesítés tiltása](howto-conditional-access-policy-block-legacy.md)\*
* [MFA megkövetelése rendszergazdák számára](howto-conditional-access-policy-admin-mfa.md)\*
* [MFA megkövetelése az Azure-felügyelethez](howto-conditional-access-policy-azure-management.md)\*
* [MFA megkövetelése minden felhasználó számára](howto-conditional-access-policy-all-users-mfa.md)\*

\*Ez a négy házirend együtt van konfigurálva, a [biztonsági alapértékek](../fundamentals/concept-fundamentals-security-defaults.md)által engedélyezett funkciókat utánozza.

## <a name="additional-policies"></a>További szabályzatok

* [Kockázatalapú feltételes hozzáférés (prémium szintű Azure AD P2 szükséges)](howto-conditional-access-policy-risk.md)
* [Megbízható helyek megkövetelése az MFA-regisztrációhoz](howto-conditional-access-policy-registration.md)
* [Hozzáférés letiltása hely alapján](howto-conditional-access-policy-location.md)
* [Megfelelő eszköz megkövetelése](howto-conditional-access-policy-compliant-device.md)
* [Hozzáférés letiltása adott alkalmazások kivételével](howto-conditional-access-policy-block-access.md)

## <a name="next-steps"></a>További lépések

- [A bejelentkezési viselkedés szimulálása a feltételes hozzáférési What If eszköz használatával.](troubleshoot-conditional-access-what-if.md)

- [A feltételes hozzáféréshez csak jelentési módot használhat az új házirend-döntések hatásának megállapításához.](concept-conditional-access-report-only.md)
