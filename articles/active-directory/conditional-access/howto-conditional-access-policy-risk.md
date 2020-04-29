---
title: Feltételes hozzáférés – kockázat alapú feltételes hozzáférés – Azure Active Directory
description: Feltételes hozzáférési szabályzatok létrehozása a szabályzatok Identity Protection-fejlesztésének engedélyezéséhez
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8054d8985596095db32d9262322d7fb0f4aab8c8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80295159"
---
# <a name="conditional-access-risk-based-conditional-access"></a>Feltételes hozzáférés: kockázatalapú feltételes hozzáférés

A prémium szintű Azure AD P2 licenccel rendelkező szervezetek létrehozhatnak Azure AD Identity Protection kockázati észleléseket tartalmazó feltételes hozzáférési szabályzatokat. Három alapértelmezett szabályzatot lehet engedélyezni a jelölőnégyzetből. 

* Az összes felhasználónak regisztrálnia kell az Azure Multi-Factor Authentication.
* Jelszó megváltoztatásának megkövetelése a nagy kockázatú felhasználók számára.
* Többtényezős hitelesítés megkövetelése közepes vagy magas bejelentkezési kockázattal rendelkező felhasználók számára.

## <a name="require-all-users-to-register-for-azure-multi-factor-authentication"></a>Minden felhasználónak regisztrálnia kell az Azure-Multi-Factor Authentication

Ha engedélyezi ezt a házirendet, az összes felhasználónak 14 napon belül regisztrálnia kell az Azure-Multi-Factor Authentication. 

1. Jelentkezzen be az **Azure Portalra**.
1. Kattintson a **Minden szolgáltatás** elemre, majd keresse meg az **Azure AD Identity Protection** szolgáltatást.
1. Kattintson az **MFA-regisztráció** lehetőségre.
1. A **hozzárendelések**alatt válassza a **felhasználók**lehetőséget.
   1. A **Belefoglalás**területen válassza a **minden felhasználó**lehetőséget.
   1. A **kizárás**területen válassza a **kizárt felhasználók kiválasztása**lehetőséget, válassza ki a szervezet vészhelyzeti hozzáférését vagy az adatbontási fiókokat, és válassza a **kiválasztás**lehetőséget. 
   1. Válassza a **Done** (Kész) lehetőséget.
1. **Házirend kényszerített** értékének beállítása **a**következőre:.
1. Kattintson a **Save** (Mentés) gombra.

## <a name="require-a-password-change-high-risk-users"></a>Jelszó módosításának megkövetelése magas kockázatú felhasználók számára

A Microsoft kutatókkal, a bűnüldözési hatóságokkal, különféle belső biztonsági csapatokkal és egyéb megbízható forrásokkal együttműködve keres felhasználónév–jelszó párokat. Ha az ilyen párok valamelyike egyezik a környezetében lévő valamelyik fiókkal, a rendszer kockázatalapú jelszómódosítást indíthat a következő szabályzat használatával.

1. Jelentkezzen be az **Azure Portalra**.
1. Kattintson a **Minden szolgáltatás** elemre, majd keresse meg az **Azure AD Identity Protection** szolgáltatást.
1. Kattintson a **felhasználói kockázati házirend**elemre.
1. A **hozzárendelések**alatt válassza a **felhasználók** lehetőséget.
   1. A **Belefoglalás**területen válassza a **minden felhasználó**lehetőséget.
   1. A **kizárás**területen válassza a **kizárt felhasználók kiválasztása**lehetőséget, válassza ki a szervezet vészhelyzeti hozzáférését vagy az adatbontási fiókokat, és válassza a **kiválasztás**lehetőséget.
   1. Válassza a **Done** (Kész) lehetőséget.
1. A **feltételek**területen válassza a **felhasználói kockázat**lehetőséget, majd válassza a **magas**lehetőséget.
   1. Kattintson a **kiválasztás** , majd a **kész**gombra.
1. A **Controls** > **hozzáférés**-vezérlés területen válassza a **hozzáférés engedélyezése**lehetőséget, majd jelölje be a **jelszó módosításának megkövetelése**jelölőnégyzetet.
   1. Kattintson a **Kiválasztás** gombra.
1. **Házirend kényszerített** értékének beállítása **a**következőre:.
1. Kattintson a **Save** (Mentés) gombra.

## <a name="require-mfa-medium-or-high-sign-in-risk-users"></a>MFA közepes vagy magas bejelentkezési kockázatú felhasználók megkövetelése

A legtöbb felhasználó viselkedése normális, amely követhető, és amikor eltérnek a normálistól, kockázatos lehet engedni, hogy egyszerűen bejelentkezzenek. Érdemes lehet letiltani ezt a felhasználót, vagy lehet, hogy csak a többtényezős hitelesítés végrehajtására kéri őket. Ha egy olyan szabályzatot szeretne engedélyezni, amely a kockázatos bejelentkezések alkalmával megköveteli a többtényezős hitelesítést, engedélyezze a következő szabályzatot.

1. Jelentkezzen be az **Azure Portalra**.
1. Kattintson a **Minden szolgáltatás** elemre, majd keresse meg az **Azure AD Identity Protection** szolgáltatást.
1. Kattintson a **bejelentkezési kockázati házirend** elemre.
1. A **hozzárendelések**alatt válassza a **felhasználók** lehetőséget.
   1. A **Belefoglalás**területen válassza a **minden felhasználó**lehetőséget.
   1. A **kizárás**területen válassza a **kizárt felhasználók kiválasztása**lehetőséget, válassza ki a szervezet vészhelyzeti hozzáférését vagy az adatbontási fiókokat, és válassza a **kiválasztás**lehetőséget.
   1. Válassza a **Done** (Kész) lehetőséget.
1. A **feltételek**területen válassza a **bejelentkezési kockázat**lehetőséget, majd válassza a **közepes vagy újabb**lehetőséget.
   1. Kattintson a **kiválasztás** , majd a **kész**gombra.
1. A **Controls** > **hozzáférés**-vezérlés területen válassza a **hozzáférés engedélyezése**lehetőséget, majd jelölje be a **többtényezős hitelesítés megkövetelése**jelölőnégyzetet.
   1. Kattintson a **Kiválasztás** gombra.
1. **Házirend kényszerített** értékének beállítása **a**következőre:.
1. Kattintson a **Save** (Mentés) gombra.

## <a name="next-steps"></a>További lépések

[Feltételes hozzáférés – közös szabályzatok](concept-conditional-access-policy-common.md)

[A hatás meghatározása a feltételes hozzáférésről szóló jelentés módban](howto-conditional-access-report-only.md)

[Bejelentkezési viselkedés szimulálása a feltételes hozzáférési What If eszköz használatával](troubleshoot-conditional-access-what-if.md)

[Az Azure Multi-Factor Authentication működése](../authentication/concept-mfa-howitworks.md)

[Mi az az Azure Active Directory Identity Protection?](../identity-protection/overview.md)
