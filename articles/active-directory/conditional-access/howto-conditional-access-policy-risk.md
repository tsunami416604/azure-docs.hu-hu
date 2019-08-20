---
title: Feltételes hozzáférés – kockázat alapú feltételes hozzáférés – Azure Active Directory
description: Feltételes hozzáférési szabályzatok létrehozása a szabályzatok Identity Protection-fejlesztésének engedélyezéséhez
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64d1b3e2f36256164420ae6b2e699f0ef48e2e78
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576549"
---
# <a name="conditional-access-risk-based-conditional-access"></a>Feltételes hozzáférés: Kockázatalapú feltételes hozzáférés

A prémium szintű Azure AD P2 licenccel rendelkező szervezetek létrehozhatnak Azure AD Identity Protection kockázati eseményeket tartalmazó feltételes hozzáférési szabályzatokat. Három alapértelmezett szabályzatot lehet engedélyezni a jelölőnégyzetből. 

* Minden felhasználónak regisztrálnia kell az Azure multi-Factor Authentication szolgáltatásban.
* Jelszó megváltoztatásának megkövetelése a nagy kockázatú felhasználók számára.
* Többtényezős hitelesítés megkövetelése közepes vagy magas bejelentkezési kockázattal rendelkező felhasználók számára.

## <a name="require-all-users-to-register-for-azure-multi-factor-authentication"></a>Az Azure multi-Factor Authentication szolgáltatás regisztrálásának megkövetelése minden felhasználó számára

Ennek a szabályzatnak az engedélyezéséhez az összes felhasználónak regisztrálnia kell az Azure multi-Factor Authentication szolgáltatásra 14 napon belül. 

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
1. A**hozzáférés**- **vezérlés** > területen válassza a **hozzáférés engedélyezése**lehetőséget, majd jelölje be a **jelszó módosításának**megkövetelése jelölőnégyzetet.
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
1. A**hozzáférés**- **vezérlés** > területen válassza a **hozzáférés engedélyezése**lehetőséget, majd jelölje be a többtényezős **hitelesítés**megkövetelése jelölőnégyzetet.
   1. Kattintson a **Kiválasztás** gombra.
1. **Házirend kényszerített** értékének beállítása **a**következőre:.
1. Kattintson a **Save** (Mentés) gombra.

## <a name="next-steps"></a>További lépések

[Feltételes hozzáférés – közös szabályzatok](concept-conditional-access-policy-common.md)

[Bejelentkezési viselkedés szimulálása a feltételes hozzáférési What If eszköz használatával](troubleshoot-conditional-access-what-if.md)

[Működés: Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)

[Mi az Azure Active Directory Identity Protection?](../identity-protection/overview.md)
