---
title: Az MFA regisztrációs szabályzatának konfigurálása – Azure Active Directory Identity Protection
description: Ismerje meg, hogyan konfigurálhatja a Azure AD Identity Protection multi-Factor Authentication regisztrációs szabályzatot.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "74382146"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>Útmutató: az Azure Multi-Factor Authentication regisztrációs szabályzatának konfigurálása

Azure AD Identity Protection segít az Azure-beli Multi-Factor Authentication (MFA) regisztrációjának kezelésében egy feltételes hozzáférési szabályzat konfigurálásával, amely az MFA-regisztráció megkövetelését írja elő, függetlenül attól, hogy milyen modern hitelesítési alkalmazásba jelentkezik be.

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>Mi az Azure Multi-Factor Authentication regisztrációs szabályzata?

Az Azure Multi-Factor Authentication lehetővé teszi annak ellenőrzését, hogy ki használ-e egynél több felhasználónevet és jelszót. Egy második biztonsági réteget biztosít a felhasználói bejelentkezésekhez. Ahhoz, hogy a felhasználók képesek legyenek válaszolni az MFA-kérésekre, először regisztrálniuk kell az Azure Multi-Factor Authentication.

Javasoljuk, hogy a felhasználói bejelentkezésekhez az Azure Multi-Factor Authentication használata szükséges, mert:

- Erős hitelesítést biztosít számos ellenőrzési lehetőség használatával.
- Kulcsszerepet játszik abban, hogy a szervezet felkészítse az Identity Protection kockázati észlelési feladatait.

További információ az Azure Multi-Factor Authenticationról: [Mi az az azure multi-Factor Authentication?](../authentication/howto-mfa-getstarted.md)

## <a name="policy-configuration"></a>Szabályzatkonfiguráció

1. Navigáljon a [Azure Portal](https://portal.azure.com).
1. Tallózással keresse meg **Azure Active Directory** > **biztonsági** > **Identity Protection** > **MFA regisztrációs szabályzatát**.
   1. A **hozzárendelések** alatt
      1. **Felhasználók** – válassza a **minden felhasználó** lehetőséget **, vagy válassza az egyének és csoportok lehetőséget,** ha korlátozza a bevezetést.
         1. Opcionálisan dönthet úgy is, hogy kizárja a felhasználókat a szabályzatból.
   1. A **vezérlők** területen
      1. Győződjön meg arról, hogy az **Azure MFA-regisztráció megkövetelése** jelölőnégyzet be van jelölve, majd válassza a **kiválasztás**lehetőséget
   1. **Szabályzat érvénybe léptetése** - **On**
   1. **Mentés**

## <a name="user-experience"></a>Felhasználó felület

Azure Active Directory Identity Protection fogja kérni a felhasználókat, hogy regisztrálják a következő bejelentkezést, amikor interaktív módon jelentkeznek be, és a regisztráció befejezéséhez 14 nap szükséges. Ez a 14 napos időszak során megkerülheti a regisztrációt, de az időszak végén, hogy a bejelentkezési folyamat elvégzése előtt regisztrálnia kell.

A kapcsolódó felhasználói élmény áttekintését lásd:

- A [Azure ad Identity Protectionekkel való bejelentkezési élmény](concept-identity-protection-user-experience.md).  

## <a name="next-steps"></a>További lépések

- [Bejelentkezési és felhasználói kockázati házirendek engedélyezése](howto-identity-protection-configure-risk-policies.md)

- [Az Azure AD önkiszolgáló jelszó-visszaállítás engedélyezése](../authentication/howto-sspr-deployment.md)

- [Az Azure Multi-Factor Authentication engedélyezése](../authentication/howto-mfa-getstarted.md)
