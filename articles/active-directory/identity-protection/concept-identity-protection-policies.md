---
title: Azure AD Identity Protection házirendek
description: Az Identity Protection által engedélyezett három szabályzat azonosítása
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
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887415"
---
# <a name="identity-protection-policies"></a>Identity Protection-házirendek

A Azure Active Directory Identity Protection három alapértelmezett szabályzatot tartalmaz, amelyeket a rendszergazdák engedélyeznek. Ezek a szabályzatok korlátozott testreszabást tartalmaznak, de a legtöbb szervezetre alkalmazhatók. Az összes házirend lehetővé teszi a felhasználók kizárását, például a [vészhelyzeti vagy a break-Glass rendszergazdai fiókjait](../users-groups-roles/directory-emergency-access.md).

![Identity Protection-házirendek](./media/concept-identity-protection-policies/identity-protection-policies.png)

## <a name="azure-mfa-registration-policy"></a>Azure MFA regisztrációs szabályzat

Az Identity Protection lehetővé teszi a szervezetek számára, hogy az Azure-Multi-Factor Authentication (MFA) a bejelentkezéskor regisztrációt igénylő feltételes hozzáférési szabályzattal nyújtsanak be. Ennek a szabályzatnak a engedélyezése nagyszerű módja annak, hogy a szervezet új felhasználói az első napon regisztrálták az MFA-t. A többtényezős hitelesítés a kockázati események egyik önszervizelési módszere az Identity Protectionben. Az önszervizelés lehetővé teszi, hogy a felhasználók saját maguk végezzék el az ügyfélszolgálati hívások mennyiségének csökkentését.

Az Azure Multi-Factor Authentication-ról további információt talál a következő cikkben, [Hogyan működik: azure multi-Factor Authentication](../authentication/concept-mfa-howitworks.md).

## <a name="sign-in-risk-policy"></a>Bejelentkezési kockázati házirend

Az Identity Protection elemzi az egyes bejelentkezések, valós idejű és offline állapotú jeleket, és kiszámítja a kockázati pontszámot azon valószínűség alapján, hogy a felhasználó nem végezte el a bejelentkezést. A rendszergazdák a kockázati pontszám alapján hozhatnak döntéseket a szervezeti követelmények betartatása érdekében. A rendszergazdák dönthetnek úgy, hogy letilthatják a hozzáférést, engedélyezik a hozzáférést, vagy engedélyezik a hozzáférést, de többtényezős hitelesítést igényelnek.

Ha a rendszer veszélyezteti a kockázatot, a felhasználók a többtényezős hitelesítést a saját kiszolgálására, valamint a kockázatos bejelentkezési esemény bezárására is használhatják a rendszergazdák számára szükségtelen zaj megelőzése érdekében.

> [!NOTE] 
> A bejelentkezési kockázati házirend elindítása előtt a felhasználóknak előzőleg regisztrálniuk kell az Azure Multi-Factor Authentication-ban.

### <a name="custom-conditional-access-policy"></a>Egyéni feltételes hozzáférési szabályzat

A rendszergazdák dönthetnek úgy is, hogy egyéni feltételes hozzáférési szabályzatot hoznak létre, beleértve a bejelentkezési kockázatokat hozzárendelési feltételként. A feltételes hozzáféréssel kapcsolatos további információkért tekintse meg a [feltételes hozzáférésről](../conditional-access/overview.md) szóló cikket.

![Egyéni feltételes hozzáférés bejelentkezési kockázati szabályzata](./media/concept-identity-protection-policies/identity-protection-custom-sign-in-policy.png)

## <a name="user-risk-policy"></a>Felhasználói kockázati házirend

Az Identity Protection kiszámíthatja, hogy mit gondol a felhasználó viselkedése, és hogyan használhatja azt a kockázatokra alapozva. A felhasználói kockázat a valószínűsége annak, hogy az identitást feltörték. A rendszergazdák a kockázati pontszám alapján hozhatnak döntéseket a szervezeti követelmények betartatása érdekében. A rendszergazdák dönthetnek úgy, hogy letiltják a hozzáférést, engedélyezik vagy engedélyezik a hozzáférést, de jelszó-módosítást igényelnek az [Azure ad önkiszolgáló jelszó-visszaállítás](../authentication/howto-sspr-deployment.md)használatával.

Ha a rendszer veszélyezteti a kockázatot, a felhasználók önkiszolgáló jelszó-visszaállítást végezhetnek, és lezárhatók a felhasználói kockázati esemény a rendszergazdák számára szükségtelen zaj megelőzése érdekében.

> [!NOTE] 
> A felhasználói kockázati házirend elindítása előtt a felhasználóknak előzőleg regisztrálniuk kell magukat az önkiszolgáló jelszó-visszaállításhoz.

## <a name="next-steps"></a>Következő lépések

- [Az Azure AD önkiszolgáló jelszó-visszaállítás engedélyezése](../authentication/howto-sspr-deployment.md)

- [Azure-Multi-Factor Authentication engedélyezése](../authentication/howto-mfa-getstarted.md)

- [Az Azure Multi-Factor Authentication regisztrációs szabályzatának engedélyezése](howto-identity-protection-configure-mfa-policy.md)

- [Bejelentkezési és felhasználói kockázati házirendek engedélyezése](howto-identity-protection-configure-risk-policies.md)
