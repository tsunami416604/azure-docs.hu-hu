---
title: Feltételes hozzáférés – Kockázatalapú feltételes hozzáférés – Azure Active Directory
description: Feltételes hozzáférési házirendek létrehozása a házirendek identitásvédelmi továbbfejlesztésének engedélyezéséhez
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295159"
---
# <a name="conditional-access-risk-based-conditional-access"></a>Feltételes hozzáférés: Kockázatalapú feltételes hozzáférés

Az Azure AD Premium P2 licenccel rendelkező szervezetek feltételes hozzáférési szabályzatokat hozhatnak létre, amelyek magukban foglalják az Azure AD Identity Protection kockázatészleléseit. Három alapértelmezett házirend engedélyezhető a dobozból. 

* Minden felhasználónak regisztrálnia kell az Azure többtényezős hitelesítéshez.
* Jelszómódosítás megkövetelése a magas kockázatú felhasználók számára.
* Többtényezős hitelesítés megkövetelése közepes vagy magas bejelentkezési kockázattal rendelkező felhasználók számára.

## <a name="require-all-users-to-register-for-azure-multi-factor-authentication"></a>Minden felhasználóregisztráció megkövetelése az Azure többtényezős hitelesítéséhez

A házirend engedélyezéséhez minden felhasználónak 14 napon belül regisztrálnia kell az Azure többtényezős hitelesítéséhez. 

1. Jelentkezzen be az **Azure Portalra.**
1. Kattintson a **Minden szolgáltatás** elemre, majd keresse meg az **Azure AD Identity Protection** szolgáltatást.
1. Kattintson az **MFA-regisztráció** lehetőségre.
1. A **Hozzárendelések**csoportban válassza a **Felhasználók**lehetőséget.
   1. A **Belefoglalás**csoportban válassza a **Minden felhasználó lehetőséget.**
   1. A **Kizárás**csoportban válassza **a Kizárt felhasználók kiválasztása**lehetőséget, válassza ki a szervezet vészelérési vagy törtüveges fiókjait, és válassza a Kijelölés **lehetőséget.** 
   1. Válassza a **Done** (Kész) lehetőséget.
1. Állítsa **a Kényszerítési házirendet** **Be beállításra.**
1. Kattintson a **Mentés** gombra.

## <a name="require-a-password-change-high-risk-users"></a>Nagy kockázatú jelszómódosítás megkövetelése

A Microsoft kutatókkal, a bűnüldözési hatóságokkal, különféle belső biztonsági csapatokkal és egyéb megbízható forrásokkal együttműködve keres felhasználónév–jelszó párokat. Ha az ilyen párok valamelyike egyezik a környezetében lévő valamelyik fiókkal, a rendszer kockázatalapú jelszómódosítást indíthat a következő szabályzat használatával.

1. Jelentkezzen be az **Azure Portalra.**
1. Kattintson a **Minden szolgáltatás** elemre, majd keresse meg az **Azure AD Identity Protection** szolgáltatást.
1. Kattintson a **Felhasználói kockázati házirendre**.
1. A **Hozzárendelések**csoportban válassza a **Felhasználók lehetőséget.**
   1. A **Belefoglalás**csoportban válassza a **Minden felhasználó lehetőséget.**
   1. A **Kizárás**csoportban válassza **a Kizárt felhasználók kiválasztása**lehetőséget, válassza ki a szervezet vészelérési vagy törtüveges fiókjait, és válassza a Kijelölés **lehetőséget.**
   1. Válassza a **Done** (Kész) lehetőséget.
1. A **Feltételek csoportban**válassza a **Felhasználói kockázat**lehetőséget, majd válassza a Magas **lehetőséget.**
   1. Kattintson **a Kijelölés,** majd **kész gombra.**
1. A **Hozzáférés vezérlése** > **csoportban**válassza **a Hozzáférés engedélyezése**lehetőséget, majd a **Jelszómódosítás megkövetelése**lehetőséget.
   1. Kattintson a **Kiválasztás** gombra.
1. Állítsa **a Kényszerítési házirendet** **Be beállításra.**
1. Kattintson a **Mentés** gombra.

## <a name="require-mfa-medium-or-high-sign-in-risk-users"></a>MFA-adathordozó- vagy magas bejelentkezési kockázati felhasználók megkövetelése

A legtöbb felhasználó viselkedése normális, amely követhető, és amikor eltérnek a normálistól, kockázatos lehet engedni, hogy egyszerűen bejelentkezzenek. Érdemes lehet blokkolni, hogy a felhasználó, vagy talán csak kérje meg őket, hogy végre többtényezős hitelesítés bizonyítani, hogy ők valóban, akik azt mondják, hogy ők. Ha egy olyan szabályzatot szeretne engedélyezni, amely a kockázatos bejelentkezések alkalmával megköveteli a többtényezős hitelesítést, engedélyezze a következő szabályzatot.

1. Jelentkezzen be az **Azure Portalra.**
1. Kattintson a **Minden szolgáltatás** elemre, majd keresse meg az **Azure AD Identity Protection** szolgáltatást.
1. Kattintson **a Bejelentkezési kockázati házirendre**
1. A **Hozzárendelések**csoportban válassza a **Felhasználók lehetőséget.**
   1. A **Belefoglalás**csoportban válassza a **Minden felhasználó lehetőséget.**
   1. A **Kizárás**csoportban válassza **a Kizárt felhasználók kiválasztása**lehetőséget, válassza ki a szervezet vészelérési vagy törtüveges fiókjait, és válassza a Kijelölés **lehetőséget.**
   1. Válassza a **Done** (Kész) lehetőséget.
1. A **Feltételek csoportban**válassza **a Bejelentkezési kockázat**lehetőséget, majd válassza a Közepes és a felett **lehetőséget.**
   1. Kattintson **a Kijelölés,** majd **kész gombra.**
1. A **Hozzáférés vezérlése** > **csoportban**válassza **a Hozzáférés engedélyezése**lehetőséget, majd a **Többtényezős hitelesítés megkövetelése**lehetőséget.
   1. Kattintson a **Kiválasztás** gombra.
1. Állítsa **a Kényszerítési házirendet** **Be beállításra.**
1. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>További lépések

[Feltételes hozzáférés közös házirendjei](concept-conditional-access-policy-common.md)

[Hatás meghatározása csak feltételes hozzáférésű jelentésmódhasználatával](howto-conditional-access-report-only.md)

[Bejelentkezési viselkedés szimulálása a Feltételes hozzáférés Mi ha eszközzel](troubleshoot-conditional-access-what-if.md)

[Az Azure Multi-Factor Authentication működése](../authentication/concept-mfa-howitworks.md)

[Mi az az Azure Active Directory Identity Protection?](../identity-protection/overview.md)
