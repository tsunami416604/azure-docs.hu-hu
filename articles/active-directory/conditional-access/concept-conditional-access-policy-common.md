---
title: Általános feltételes hozzáférési házirendek – Azure Active Directory
description: Gyakran használt feltételes hozzáférési szabályzatok a szervezeteknél
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbf4d7b9f622ff590eee401544bf62655fd8d096
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86024383"
---
# <a name="common-conditional-access-policies"></a>Gyakori feltételes hozzáférési szabályzatok

A [biztonsági alapértékek](../fundamentals/concept-fundamentals-security-defaults.md) kiválóan használhatók, de számos szervezetnek nagyobb rugalmasságra van szüksége az általuk kínáltnál. Például számos szervezetnek képesnek kell lennie arra, hogy bizonyos fiókokat kizárjon, például vészhelyzeti hozzáférési vagy bomlás-üveg felügyeleti fiókjait a többtényezős hitelesítést igénylő feltételes hozzáférési szabályzatokból. Ezekben a szervezeteknél a cikkben hivatkozott általános szabályzatok is használhatók.

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

\*Ez a négy házirend együtt van konfigurálva, a [biztonsági Alapértelmezések](../fundamentals/concept-fundamentals-security-defaults.md)által engedélyezett funkciókat utánozza.

## <a name="additional-policies"></a>További szabályzatok

* [A kockázatalapú feltételes hozzáférésre vonatkozó bejelentkezés (prémium szintű Azure AD P2 szükséges)](howto-conditional-access-policy-risk.md)
* [Felhasználói kockázat alapú feltételes hozzáférés (prémium szintű Azure AD P2 szükséges)](howto-conditional-access-policy-risk-user.md)
* [Megbízható helyek megkövetelése az MFA-regisztrációhoz](howto-conditional-access-policy-registration.md)
* [Hozzáférés letiltása hely szerint](howto-conditional-access-policy-location.md)
* [Megfelelő eszköz megkövetelése](howto-conditional-access-policy-compliant-device.md)
* [Hozzáférés letiltása adott alkalmazások kivételével](howto-conditional-access-policy-block-access.md)

## <a name="next-steps"></a>További lépések

- [A bejelentkezési viselkedés szimulálása a feltételes hozzáférési What If eszköz használatával.](troubleshoot-conditional-access-what-if.md)

- [A feltételes hozzáféréshez csak jelentési módot használhat az új házirend-döntések hatásának megállapításához.](concept-conditional-access-report-only.md)
