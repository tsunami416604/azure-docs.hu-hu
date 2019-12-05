---
title: Általános feltételes hozzáférési házirendek – Azure Active Directory
description: Gyakran használt feltételes hozzáférési szabályzatok a szervezeteknél
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf338ad0c555038cd99c3604d69ab80371d6ea22
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74804974"
---
# <a name="common-conditional-access-policies"></a>Gyakori feltételes hozzáférési szabályzatok

Az alapkonfiguráció-védelmi szabályzatok nagyszerűek, de számos szervezetnek nagyobb rugalmasságra van szüksége az általuk kínáltnál. Például számos szervezetnek képesnek kell lennie arra, hogy bizonyos fiókokat kizárjon, például vészhelyzeti hozzáférési vagy bomlás-üveg felügyeleti fiókjait a többtényezős hitelesítést igénylő feltételes hozzáférési szabályzatokból. Ezekben a szervezeteknél a cikkben hivatkozott általános szabályzatok is használhatók.

![Feltételes hozzáférési szabályzatok a Azure Portal](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>Vészhelyzeti hozzáférési fiókok

További információ a segélyhívó fiókokról és azok fontos okairól a következő cikkekben talál további információt: 

* [Vészhelyzeti hozzáférési fiókok kezelése az Azure AD-ben](../users-groups-roles/directory-emergency-access.md)
* [Rugalmas hozzáférés-vezérlési felügyeleti stratégia létrehozása Azure Active Directory](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>A szervezetek által központilag telepített általános házirendek

* [MFA megkövetelése rendszergazdák számára](howto-conditional-access-policy-admin-mfa.md)
* [MFA megkövetelése az Azure-felügyelethez](howto-conditional-access-policy-azure-management.md)
* [MFA megkövetelése minden felhasználó számára](howto-conditional-access-policy-all-users-mfa.md)
* [Örökölt hitelesítés tiltása](howto-conditional-access-policy-block-legacy.md)
* [Kockázatalapú feltételes hozzáférés (prémium szintű Azure AD P2 szükséges)](howto-conditional-access-policy-risk.md)
* [Megbízható hely megkövetelése az MFA-regisztrációhoz](howto-conditional-access-policy-registration.md)
* [Hozzáférés letiltása hely szerint](howto-conditional-access-policy-location.md)
* [Megfelelő eszköz megkövetelése](howto-conditional-access-policy-compliant-device.md)

## <a name="next-steps"></a>Következő lépések

[Bejelentkezési viselkedés szimulálása a feltételes hozzáférési What If eszköz használatával](troubleshoot-conditional-access-what-if.md)
