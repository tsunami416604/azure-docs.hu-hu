---
title: A multi-Factor Authentication regisztrációs szabályzatának konfigurálása a Azure Active Directory Identity Protectionban | Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja a Azure AD Identity Protection multi-Factor Authentication regisztrációs szabályzatot.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/01/2019
author: MicrosoftGuyJFlo
manager: daveba
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 939f08fd16cf27e641cf6436a00396ad2db8e6c3
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126390"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>kézikönyv: Az Azure Multi-Factor Authentication regisztrációs szabályzatának konfigurálása

A Azure AD Identity Protection segítségével a többtényezős hitelesítés (MFA) regisztrációját úgy kezelheti, hogy a feltételes hozzáférési szabályzatot úgy konfigurálja, hogy az MFA-regisztráció megkövetelése nélkül milyen modern hitelesítési alkalmazást szeretne bejelentkezni. Ez a cikk bemutatja, hogyan használható a szabályzat, és hogyan konfigurálható.



## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>Mi az Azure Multi-Factor Authentication regisztrációs szabályzata?

Az Azure Multi-Factor Authentication lehetővé teszi annak ellenőrzését, hogy ki használ-e egynél több felhasználónevet és jelszót. Egy második biztonsági réteget biztosít a felhasználói bejelentkezésekhez. Ahhoz, hogy a felhasználók képesek legyenek válaszolni az MFA-kérésekre, először regisztrálniuk kell az Azure Multi-Factor Authentication.

Javasoljuk, hogy a felhasználói bejelentkezésekhez az Azure Multi-Factor Authentication használata szükséges, mert:

- Erős hitelesítést biztosít számos egyszerű ellenőrzési lehetőséggel
- Kulcsszerepet játszik a szervezet előkészítésében, hogy megvédje és helyreállítsa a kockázati észleléseket az Identity Protectionben

Az MFA-ról további részleteket a [Mi az Azure multi-Factor Authentication?](../authentication/howto-mfa-getstarted.md) című témakörben talál.

## <a name="how-do-i-access-the-registration-policy"></a>Hogyan hozzáférni a regisztrációs szabályzathoz?

Az MFA regisztrációs szabályzata a [Azure ad Identity Protection lap](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy) **configure (Konfigurálás** ) szakaszában található.

![MFA-szabályzat](./media/howto-mfa-policy/1014.png)

## <a name="policy-settings"></a>Szabályzatbeállítások

Az MFA regisztrációs szabályzatának konfigurálásakor a következő konfigurációs módosításokat kell elvégeznie:

- Azok a felhasználók és csoportok, amelyekre a szabályzat vonatkozik. Ne felejtse el kizárni a szervezet [vészhelyzeti hozzáférési fiókjait](../users-groups-roles/directory-emergency-access.md).

    ![Felhasználók és csoportok](./media/howto-mfa-policy/11.png)

- A kényszeríteni kívánt vezérlő – **Azure MFA-regisztráció** megkövetelése

    ![Access](./media/howto-mfa-policy/12.png)

- **A**kényszerített szabályzatot be kell állítani.

    ![Szabályzat érvénybe léptetése](./media/howto-mfa-policy/14.png)

- Szabályzat **mentése**

## <a name="user-experience"></a>Felhasználói élmény

Azure Active Directory Identity Protection fogja kérni a felhasználókat, hogy regisztrálják magukat a következő alkalommal, amikor interaktív módon jelentkeznek be.

A kapcsolódó felhasználói élmény áttekintését lásd:

- [Multi-Factor Authentication regisztrációs folyamat](flows.md#multi-factor-authentication-registration).  
- A [Azure ad Identity Protectionekkel való bejelentkezési élmény](flows.md).  

## <a name="next-steps"></a>További lépések

A Azure AD Identity Protection áttekintéséhez tekintse meg a [Azure ad Identity Protection áttekintését](overview.md).
