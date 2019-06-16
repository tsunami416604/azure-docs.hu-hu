---
title: A többtényezős hitelesítési regisztrációs házirend konfigurálása az Azure Active Directory Identity Protection |} A Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja az Azure AD Identity Protection többtényezős hitelesítési regisztrációs házirend.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: article
ms.date: 05/01/2019
author: MicrosoftGuyJFlo
manager: daveba
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 434d07163713a139b42a5dbe1664f81dafc2a1ca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108945"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>kézikönyv: Az Azure multi-factor Authentication regisztrációs szabályzat konfigurálása

Az Azure AD Identity Protection segít az üzembe helyezést, a többtényezős hitelesítés (MFA) regisztrációs kezelését, függetlenül attól, hogy mely alkalmazás a bejelentkezés MFA-regisztráció megkövetelése feltételes hozzáférési szabályzat konfigurálása. Ez a cikk leírja, mi a házirend használható és konfigurálásának.

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>Mi az Azure multi-factor Authentication regisztrációs szabályzat?

Az Azure multi-factor Authentication biztosít, akik ellenőrizheti azt jelenti, hogy több mint csupán felhasználónévvel és jelszóval. Biztosít egy második biztonsági szintként, a felhasználói bejelentkezéseket. Ahhoz, hogy a felhasználók tudják MFA válaszolniuk akkor először regisztrálnia kell az Azure multi-factor Authentication.

Azt javasoljuk, hogy az Azure multi-factor Authentication a felhasználók bejelentkezési folyamatába igényel el, mert azt:

- Szolgáltatás egyszerű ellenőrzési lehetőség számos szigorú hitelesítést végez
- A szervezet védelme és helyreállítása a kockázati eseményekről, az Identity Protection előkészítése a kulcsfontosságú szerepet játszik az

A többtényezős hitelesítés további részletekért lásd: [Mi az Azure multi-factor Authentication?](../authentication/howto-mfa-getstarted.md)

## <a name="how-do-i-access-the-registration-policy"></a>Hogyan érhetem el a regisztrációs házirendet?

Az MFA regisztrációs szabályzatának szerepel a **konfigurálása** szakaszában a [Azure AD Identity Protection lapról](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).

![Többtényezős hitelesítési szabályzat](./media/howto-mfa-policy/1014.png)

## <a name="policy-settings"></a>Szabályzatbeállítások

Az MFA regisztrációs szabályzatának konfigurálásakor kell a következő konfigurációs módosításokat:

- A felhasználók és csoportok, a szabályzat vonatkozik. Ne felejtse el a szervezet kizárása [vészelérési fiókok](../users-groups-roles/directory-emergency-access.md).

    ![Felhasználók és csoportok](./media/howto-mfa-policy/11.png)

- A vezérlő kikényszerítéséhez - **igényel az Azure MFA-regisztráció**

    ![Access](./media/howto-mfa-policy/12.png)

- Kényszerítése kell beállítani a házirend **a**.

    ![Szabályzat kényszerítése](./media/howto-mfa-policy/14.png)

- **Mentés** a szabályzat

## <a name="user-experience"></a>Felhasználói élmény

A kapcsolódó felhasználói szolgáltatások áttekintését lásd:

- [A multi-factor authentication regisztrációs folyamat](flows.md#multi-factor-authentication-registration).  
- [Bejelentkezési élmény az Azure AD Identity Protection](flows.md).  

## <a name="next-steps"></a>További lépések

Az Azure AD Identity Protection áttekintést kaphat, tekintse meg a [áttekintése az Azure AD Identity Protection](overview.md).
