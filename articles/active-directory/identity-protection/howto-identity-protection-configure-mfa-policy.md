---
title: A multi-Factor Authentication regisztrációs szabályzatának konfigurálása a Azure Active Directory Identity Protectionban
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
ms.openlocfilehash: bc98f645c6b24069e090560a049ccb4fcd03dfec
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887571"
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

1. Lépjen az [Azure Portalra](https://portal.azure.com).
1. Keresse meg **Azure Active Directory** > **biztonsági** > **Identity Protection** > **MFA regisztrációs szabályzatot**.
   1. A **hozzárendelések** alatt
      1. **Felhasználók** – válassza a **minden felhasználó** lehetőséget **, vagy válassza az egyének és csoportok lehetőséget,** ha korlátozza a bevezetést.
         1. Opcionálisan dönthet úgy is, hogy kizárja a felhasználókat a szabályzatból.
   1. A **vezérlők** területen
      1. Győződjön meg arról, hogy az **Azure MFA-regisztráció megkövetelése** jelölőnégyzet be van jelölve, majd válassza a **kiválasztás**lehetőséget
   1. Házirend **- - ** **érvénybe léptetése**
   1. **Mentés**

## <a name="user-experience"></a>Felhasználói élmény

Azure Active Directory Identity Protection fogja kérni a felhasználókat, hogy regisztrálják a következő bejelentkezést, amikor interaktív módon jelentkeznek be, és a regisztráció befejezéséhez 14 nap szükséges. Ez a 14 napos időszak során megkerülheti a regisztrációt, de az időszak végén, hogy a bejelentkezési folyamat elvégzése előtt regisztrálnia kell.

A kapcsolódó felhasználói élmény áttekintését lásd:

- A [Azure ad Identity Protectionekkel való bejelentkezési élmény](concept-identity-protection-user-experience.md).  

## <a name="next-steps"></a>Következő lépések

- [Bejelentkezési és felhasználói kockázati házirendek engedélyezése](howto-identity-protection-configure-risk-policies.md)

- [Az Azure AD önkiszolgáló jelszó-visszaállítás engedélyezése](../authentication/howto-sspr-deployment.md)

- [Azure-Multi-Factor Authentication engedélyezése](../authentication/howto-mfa-getstarted.md)
