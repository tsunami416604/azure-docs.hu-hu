---
title: Az MFA regisztrációs házirendjének konfigurálása – Azure Active Directory-identitásvédelem
description: Ismerje meg, hogyan konfigurálhatja az Azure AD Identity Protection többtényezős hitelesítésregisztrációs szabályzatot.
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
ms.openlocfilehash: 2fa6a4cf184b426355f62117ea51642127eee529
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382146"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>Útmutató: Konfigurálja az Azure többtényezős hitelesítésregisztrációs szabályzatát

Az Azure AD Identity Protection segít az Azure multi-factor authentication (MFA) regisztrációjának bevezetésének kezelésében azáltal, hogy feltételes hozzáférési szabályzatot konfigurál, hogy az MFA-regisztrációt igényelje, függetlenül attól, hogy milyen modern hitelesítési alkalmazásba jelentkezik be.

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>Mi az Azure többtényezős hitelesítésregisztrációs szabályzata?

Az Azure többtényezős hitelesítés e felhasználónévnél és jelszónál többet is biztosít annak ellenőrzéséhez, hogy kit használ. Ez biztosítja a második biztonsági réteget a felhasználói bejelentkezések. Annak érdekében, hogy a felhasználók válaszolhassanak az MFA-kérésekre, először regisztrálniuk kell az Azure többtényezős hitelesítéshez.

Azt javasoljuk, hogy a felhasználói bejelentkezések miatt az Azure többtényezős hitelesítést igényeljen:

- Erős hitelesítést biztosít számos ellenőrzési lehetőségen keresztül.
- Kulcsfontosságú szerepet játszik a szervezet felkészítése az identitásvédelem ben a kockázatészlelésből való öngyógyításra.

Az Azure többtényezős hitelesítésről a [Mi az Azure többtényezős hitelesítése című](../authentication/howto-mfa-getstarted.md) témakörben talál további információt?

## <a name="policy-configuration"></a>Szabályzatkonfiguráció

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com)
1. Tallózással keresse meg az **Azure Active Directory** > **biztonsági** > **identitásvédelem** > **mfa regisztrációs házirendje.**
   1. Hozzárendelések **csoportban**
      1. **Felhasználók** – Válassza a **Minden felhasználó** vagy A személyek és csoportok **kiválasztása lehetőséget,** ha korlátozza a bevezetést.
         1. Tetszés szerint kizárhatja a felhasználókat a házirendből.
   1. A **Vezérlők csoportban**
      1. Győződjön meg arról, hogy az **Azure MFA-regisztráció megkövetelése** jelölőnégyzet be van jelölve, és válassza **a Kijelölés lehetőséget.**
   1. **Házirend kényszerítése** - **On**
   1. **Mentés**

## <a name="user-experience"></a>Felhasználó felület

Az Azure Active Directory identity Protection kérni fogja a felhasználókat, hogy regisztráljon a következő alkalommal, amikor bejelentkezik interaktívan, és 14 nap a regisztráció befejezéséhez. Ebben a 14 napos időszakban megkerülhetik a regisztrációt, de az időszak végén regisztrálniuk kell őket a bejelentkezési folyamat befejezése előtt.

A kapcsolódó felhasználói élmény áttekintését az: (A felhasználók számára a következő) témakörben találja:

- [Bejelentkezési élmény az Azure AD Identity Protection szolgáltatással.](concept-identity-protection-user-experience.md)  

## <a name="next-steps"></a>További lépések

- [Bejelentkezési és felhasználói kockázati házirendek engedélyezése](howto-identity-protection-configure-risk-policies.md)

- [Az Azure AD önkiszolgáló jelszó-visszaállítás engedélyezése](../authentication/howto-sspr-deployment.md)

- [Az Azure Multi-Factor Authentication engedélyezése](../authentication/howto-mfa-getstarted.md)
