---
title: MFA vagy 2FA és kiemelt identitáskezelés – Azure AD | Microsoft dokumentumok
description: Ismerje meg, hogyan ellenőrzi az Azure AD kiemelt identitáskezelés (PIM) a többtényezős hitelesítést (MFA).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6df593909c3ae5962e413eb84e64196fade0326b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022142"
---
# <a name="multi-factor-authentication-and-privileged-identity-management"></a>Többtényezős hitelesítés és kiemelt identitáskezelés

Azt javasoljuk, hogy többtényezős hitelesítést (MFA) szeretne minden rendszergazdához. Ez csökkenti a sérült jelszó miatti támadások kockázatát.

Megkövetelheti, hogy a felhasználók bejelentkezéskor végezzenek el egy többtényezős hitelesítési kihívást. Azt is megkövetelheti, hogy a felhasználók egy többtényezős hitelesítési kihívást hajtsanak végre, amikor aktiválnak egy szerepkört az Azure Active Directory (Azure AD) kiemelt identitáskezelés (PIM) aktiválásakor. Így ha a felhasználó nem teljesítette a többtényezős hitelesítési kihívást, amikor bejelentkezett, a rendszer kéri, hogy ezt a kiemelt identitáskezelés.

> [!IMPORTANT]
> Jelenleg az Azure többtényezős hitelesítése csak munkahelyi vagy iskolai fiókokkal működik, a Microsoft személyes fiókjaival (általában olyan személyes fiókkal, amely a Microsoft-szolgáltatásokba, például a Skype-ba, az Xboxba vagy a Outlook.com való bejelentkezéshez használatos). Emiatt a személyes fiókot használó személyek nem lehetnek jogosult rendszergazdai jogosultságúak, mert nem használhatják a többtényezős hitelesítést a szerepkörök aktiválásához. Ha ezeknek a felhasználóknak továbbra is microsoftos fiókkal kell kezelniük a munkaterheléseket, egyelőre emeljük őket állandó rendszergazdákra.

## <a name="how-pim-validates-mfa"></a>Hogyan ellenőrzi a PIM az MFA-t?

A többtényezős hitelesítés érvényesítésének két lehetősége van, amikor a felhasználó aktivál egy szerepkört.

A legegyszerűbb megoldás az Azure többtényezős hitelesítése a kiemelt szerepkört aktiváló felhasználók számára. Ehhez először ellenőrizze, hogy ezek a felhasználók rendelkeznek-e licenccel, ha szükséges, és regisztrált az Azure többtényezős hitelesítés. Az Azure többtényezős hitelesítésének üzembe helyezéséről a [Felhőalapú Azure többtényezős hitelesítés telepítése](../authentication/howto-mfa-getstarted.md)című témakörben talál további információt. Ajánlott, de nem szükséges, hogy konfigurálja az Azure AD kényszerítése többtényezős hitelesítés ta-felhasználók, amikor bejelentkeznek. Ennek az az oka, hogy a többtényezős hitelesítési ellenőrzéseket maga a Kiemelt identitáskezelés fogja végezni.

Azt is megteheti, ha a felhasználók hitelesítik a helyszíni lehet, hogy az identitásszolgáltató felelős a többtényezős hitelesítés. Ha például úgy konfigurálta az AD összevonási szolgáltatásokat, hogy az Azure AD elérése előtt intelligenskártyás hitelesítést igényeljen, [a felhőbeli erőforrások védelme az Azure multi-factor hitelesítéssel és az AD FS szolgáltatással](../authentication/howto-mfa-adfs.md) utasításokat tartalmaz az AD FS azure AD-nek történő küldéséhez való konfigurálására. Amikor egy felhasználó megpróbál aktiválni egy szerepkört, a kiemelt identitáskezelés elfogadja, hogy a többtényezős hitelesítés már érvényesítve lett a felhasználó számára, amint megkapja a megfelelő jogcímeket.

## <a name="next-steps"></a>További lépések

- [Az Azure AD szerepkör-beállításainak konfigurálása a kiemelt identitáskezelésben](pim-how-to-change-default-settings.md)
- [Az Azure-erőforrásszerepkör-beállítások konfigurálása a Kiemelt identitáskezelés ben](pim-resource-roles-configure-role-settings.md)
