---
title: Multi-Factor Authentication (MFA) és PIM – Azure Active Directory | Microsoft Docs
description: Ismerje meg, hogyan ellenőrzi a Azure AD Privileged Identity Management (PIM) a többtényezős hitelesítést (MFA).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 08/31/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 686c39c9fb1f1ff7c0ecf068a6612f530620d5dc
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804297"
---
# <a name="multi-factor-authentication-mfa-and-pim"></a>Multi-Factor Authentication (MFA) és PIM

Javasoljuk, hogy a többtényezős hitelesítés (MFA) megkövetelése az összes rendszergazda számára. Ez csökkenti a támadás kockázatát egy sérült jelszó miatt.

Megkövetelheti, hogy a felhasználók a bejelentkezéskor MFA-kihívásokat végezzenek el. Azt is megkövetelheti, hogy a felhasználók a Azure Active Directory (Azure AD) Privileged Identity Management (PIM) szerepkör aktiválásakor végezzenek el MFA-kihívásokat. Így ha a felhasználó nem fejezte be az MFA-t, amikor bejelentkezett, a rendszer kérni fogja a PIM által.

> [!IMPORTANT]
> Az Azure MFA jelenleg csak munkahelyi vagy iskolai fiókokkal működik, nem Microsoft-fiókokkal (általában személyes fiókkal, amely a Microsoft-szolgáltatásokhoz, például a Skype, az Xbox, a Outlook.com stb.) való bejelentkezéshez használatos. Ennek következtében bárki, aki Microsoft-fiókt használ, nem lehet jogosult rendszergazda, mert nem használhatják az MFA-t a szerepköreik aktiválásához. Ha ezeknek a felhasználóknak továbbra is a munkaterhelések felügyeletét Microsoft-fiók segítségével kell megadniuk, akkor az állandó rendszergazdák számára is megemelheti őket.

## <a name="how-pim-validates-mfa"></a>Hogyan érvényesíti a PIM az MFA-t

Az MFA érvényesítésére két lehetőség áll rendelkezésre, ha egy felhasználó aktiválja a szerepkört.

A legegyszerűbb lehetőség arra, hogy az Azure MFA-t használja a Kiemelt szerepkört aktiváló felhasználók számára. Ehhez először ellenőrizze, hogy ezek a felhasználók jogosultak-e, ha szükséges, és regisztrálva vannak az Azure MFA-hoz. További információ az Azure MFA üzembe helyezéséről: [Cloud-based azure multi-Factor Authentication üzembe helyezése](../authentication/howto-mfa-getstarted.md). Ajánlott, de nem kötelező, hogy az Azure AD-t úgy konfigurálja, hogy az MFA-t kikényszerítse ezen felhasználók számára a bejelentkezéskor. Ennek az az oka, hogy az MFA-ellenőrzéseket maga a PIM fogja elvégezni.

Ha a felhasználók helyszíni hitelesítést végeznek, akkor az identitás-szolgáltató felelős az MFA-nak. Ha például úgy konfigurálta az AD összevonási szolgáltatásokat, hogy az Azure AD-hez való hozzáférés előtt intelligenskártya-alapú hitelesítést igényeljen, a [felhőalapú erőforrásokat az azure multi-Factor Authentication és a AD FS](../authentication/howto-mfa-adfs.md) a AD FS konfigurálására vonatkozó utasításokat tartalmaz. jogcímek küldése az Azure AD-nek. Amikor egy felhasználó megpróbál aktiválni egy szerepkört, a PIM elfogadja, hogy az MFA már érvényesítve lett a felhasználó számára, miután megkapja a megfelelő jogcímeket.

## <a name="next-steps"></a>További lépések

- [Az Azure AD szerepkör beállításainak konfigurálása a PIM-ben](pim-how-to-change-default-settings.md)
- [Az Azure erőforrás-szerepkör beállításainak konfigurálása a PIM-ben](pim-resource-roles-configure-role-settings.md)
