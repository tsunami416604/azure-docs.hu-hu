---
title: MFA vagy 2FA és Privileged Identity Management – Azure AD | Microsoft Docs
description: Ismerje meg, hogyan ellenőrzi a Azure AD Privileged Identity Management (PIM) a többtényezős hitelesítést (MFA).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf1113f7b2f396deed849fa46108537f290b53a1
ms.sourcegitcommit: 24f31287b6a526e23ff5b5469113522d1ccd4467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/12/2020
ms.locfileid: "84742096"
---
# <a name="multi-factor-authentication-and-privileged-identity-management"></a>Multi-Factor Authentication és Privileged Identity Management

Javasoljuk, hogy a többtényezős hitelesítés (MFA) megkövetelése az összes rendszergazda számára. Ez csökkenti a támadás kockázatát egy sérült jelszó miatt.

Megkövetelheti, hogy a felhasználók a bejelentkezéskor egy multi-Factor Authentication-kihívást végezzenek. Azt is megkövetelheti, hogy a felhasználók a Azure Active Directory (Azure AD) Privileged Identity Management (PIM) szerepkör aktiválásakor végezzenek el egy multi-Factor Authentication-kihívást. Így ha a felhasználó nem adott meg többtényezős hitelesítési kihívást, amikor bejelentkezett, a rendszer Privileged Identity Management fogja kérni őket.

> [!IMPORTANT]
> Az Azure Multi-Factor Authentication csak munkahelyi vagy iskolai fiókokkal működik, nem a Microsoft személyes fiókjaival (általában személyes fiókkal, amely a Microsoft-szolgáltatásokba (például Skype, Xbox vagy Outlook.com) való bejelentkezéshez használatos. Emiatt a személyes fiókot használó felhasználók nem jogosultak a rendszergazda szerepkörre, mert nem használhatják a többtényezős hitelesítést a szerepköreik aktiválásához. Ha ezeknek a felhasználóknak továbbra is a munkaterhelések felügyeletét Microsoft-fiók segítségével kell megadniuk, akkor az állandó rendszergazdák számára is megemelheti őket.

## <a name="how-pim-validates-mfa"></a>Hogyan érvényesíti a PIM az MFA-t

A többtényezős hitelesítés érvényesítésére két lehetőség áll rendelkezésre, amikor egy felhasználó aktiválja a szerepkört.

A legegyszerűbb lehetőség az Azure Multi-Factor Authentication használata a Kiemelt szerepkört aktiváló felhasználók számára. Ehhez először ellenőrizze, hogy ezek a felhasználók jogosultak-e, ha szükséges, és regisztrálva vannak az Azure Multi-Factor Authentication. Az Azure Multi-Factor Authentication üzembe helyezésével kapcsolatos további információkért lásd: [felhőalapú Azure-multi-Factor Authentication üzembe helyezése](../authentication/howto-mfa-getstarted.md). Ajánlott, de nem kötelező, hogy az Azure AD-t konfigurálja a többtényezős hitelesítés érvényesítéséhez ezen felhasználók számára a bejelentkezéskor. Ennek az az oka, hogy a multi-Factor Authentication-ellenőrzéseket Privileged Identity Management saját maga végzi.

Ha a felhasználók helyszíni hitelesítést végeznek, akkor a többtényezős hitelesítésért a személyazonossági szolgáltató felelős. Ha például úgy konfigurálta az Active Directory összevonási szolgáltatásokat, hogy az Azure AD elérése előtt intelligenskártya-alapú hitelesítést igényeljen, a [felhőalapú erőforrások Azure-multi-Factor Authentication való biztonságossá tétele és AD FS](../authentication/howto-mfa-adfs.md) a AD FS konfigurálására vonatkozó utasításokat tartalmaz a jogcímek Azure ad-be való küldéséhez. Amikor egy felhasználó megpróbál aktiválni egy szerepkört, Privileged Identity Management elfogadja, hogy a többtényezős hitelesítés már érvényesítve lett a felhasználó számára, amint megkapja a megfelelő jogcímeket.

## <a name="next-steps"></a>További lépések

- [Azure AD-szerepkör beállításainak konfigurálása Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Az Azure erőforrás-szerepkör beállításainak konfigurálása Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
