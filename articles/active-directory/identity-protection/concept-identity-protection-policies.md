---
title: Az Azure AD identitásvédelem szabályzatai
description: Az identitásvédelemmel engedélyezett három házirend azonosítása
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a44e32efa3e38cf9c4df7dc00e3189c129db418
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72887415"
---
# <a name="identity-protection-policies"></a>Identity Protection-szabályzatok

Az Azure Active Directory identity protection három alapértelmezett szabályzatot tartalmaz, amelyeket a rendszergazdák engedélyezhetnek. Ezek a házirendek korlátozott testreszabást tartalmaznak, de a legtöbb szervezetre vonatkoznak. Minden házirend lehetővé teszi a felhasználók kizárását, például a [segélyhívó vagy a törésüveg-rendszergazdai fiókokat.](../users-groups-roles/directory-emergency-access.md)

![Identity Protection-szabályzatok](./media/concept-identity-protection-policies/identity-protection-policies.png)

## <a name="azure-mfa-registration-policy"></a>Az Azure MFA regisztrációs szabályzata

Az Identity Protection segítségével a szervezetek bevezethetik az Azure többtényezős hitelesítést (MFA) egy feltételes hozzáférési szabályzat használatával, amely regisztrációt igényel a bejelentkezéskor. A házirend engedélyezése nagyszerű módja annak, hogy a szervezet új felhasználói az első napon regisztráltak az MFA-ra. A többtényezős hitelesítés az identitásvédelemen belüli kockázati események önszervizelési módszereinek egyike. Az önszervizelés lehetővé teszi a felhasználók számára, hogy saját maguk is tegyenek lépéseket az ügyfélszolgálati hívásmennyiség csökkentése érdekében.

Az Azure többtényezős hitelesítéséről további információ a Következő cikkben [található: Hogyan működik: Azure többtényezős hitelesítés.](../authentication/concept-mfa-howitworks.md)

## <a name="sign-in-risk-policy"></a>Bejelentkezési kockázati szabályzat

Az Identity Protection elemzi az egyes bejelentkezések jeleit, valós idejű és offline egyaránt, és kiszámítja a kockázati pontszámot annak valószínűsége alapján, hogy a bejelentkezést nem a felhasználó hajtotta végre. A rendszergazdák a kockázati pontszám jelzés alapján hozhatnak döntést a szervezeti követelmények kényszerítése érdekében. A rendszergazdák letilthatják a hozzáférést, engedélyezik a hozzáférést, vagy engedélyezik a hozzáférést, de többtényezős hitelesítést igényelnek.

Ha kockázatot észlel, a felhasználók többtényezős hitelesítést végezhetnek az önjavításhoz, és bezárhatják a kockázatos bejelentkezési eseményt, hogy megakadályozzák a rendszergazdák szükségtelen zaját.

> [!NOTE] 
> A felhasználóknak korábban regisztrálniuk kell az Azure többtényezős hitelesítéshez a bejelentkezési kockázati szabályzat aktiválása előtt.

### <a name="custom-conditional-access-policy"></a>Egyéni feltételes hozzáférési házirend

A rendszergazdák egyéni feltételes hozzáférési házirendet is létrehozhatnak, beleértve a bejelentkezési kockázatot hozzárendelési feltételként. További információ a feltételes hozzáférésről a [Mi a feltételes hozzáférés?](../conditional-access/overview.md)

![Egyéni feltételes hozzáférés bejelentkezési kockázati szabályzat](./media/concept-identity-protection-policies/identity-protection-custom-sign-in-policy.png)

## <a name="user-risk-policy"></a>Felhasználói kockázati házirend

Az Identitásvédelem kiszámíthatja, hogy szerinte mi a normális a felhasználó viselkedése esetén, és ezt a kockázatra alapozhatja. A felhasználói kockázat az identitás feltörésének valószínűségének kiszámítása. A rendszergazdák a kockázati pontszám jelzés alapján hozhatnak döntést a szervezeti követelmények kényszerítése érdekében. A rendszergazdák letilthatják a hozzáférést, engedélyezhetik a hozzáférést, vagy engedélyezhetik a hozzáférést, de jelszómódosítást kérhetnek az [Azure AD önkiszolgáló jelszó-visszaállításával.](../authentication/howto-sspr-deployment.md)

Ha kockázatot észlel, a felhasználók önkiszolgáló jelszó-visszaállítást hajthatnak végre önjavítóra, és bezárhatják a felhasználói kockázati eseményt, hogy megakadályozzák a szükségtelen zajt a rendszergazdák számára.

> [!NOTE] 
> A felhasználói kockázati házirend aktiválása előtt a felhasználóknak korábban regisztrálniuk kell az önkiszolgáló jelszó-alaphelyzetbe állításra.

## <a name="next-steps"></a>További lépések

- [Az Azure AD önkiszolgáló jelszó-visszaállítás engedélyezése](../authentication/howto-sspr-deployment.md)

- [Az Azure Multi-Factor Authentication engedélyezése](../authentication/howto-mfa-getstarted.md)

- [Az Azure többtényezős hitelesítésregisztrációs házirendjének engedélyezése](howto-identity-protection-configure-mfa-policy.md)

- [Bejelentkezési és felhasználói kockázati házirendek engedélyezése](howto-identity-protection-configure-risk-policies.md)
