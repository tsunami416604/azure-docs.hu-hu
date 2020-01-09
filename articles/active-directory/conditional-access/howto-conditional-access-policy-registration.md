---
title: Feltételes hozzáférés – kombinált biztonsági információk – Azure Active Directory
description: Egyéni feltételes hozzáférési szabályzat létrehozása a biztonsági adatok regisztrálásához szükséges megbízható hely megköveteléséhez
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c9b01cc06b3d0ef8f47b34e9ef86bec9adac03f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424848"
---
# <a name="conditional-access-require-trusted-location-for-mfa-registration"></a>Feltételes hozzáférés: megbízható hely megkövetelése az MFA-regisztrációhoz

Az Azure Multi-Factor Authentication és az önkiszolgáló jelszó-visszaállítás felhasználó általi regisztrálásának engedélyezése a felhasználói műveletekhez a feltételes hozzáférési házirendben. Ez az előzetes verziójú funkció olyan szervezetek számára érhető el, akik engedélyezték a [közös regisztráció előzetes](../authentication/concept-registration-mfa-sspr-combined.md)verzióját. Ez a funkció olyan szervezeteknél engedélyezhető, ahol olyan feltételeket kívánnak használni, mint például a megbízható hálózati hely az Azure-Multi-Factor Authentication és SSPR való regisztrációhoz való hozzáférés korlátozása érdekében. A megbízható helyek feltételes hozzáférésben való létrehozásával kapcsolatos további információkért tekintse meg a következő cikket: [Mi a hely feltétele a feltételes hozzáférés Azure Active Directory?](../conditional-access/location-condition.md#named-locations)

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Szabályzat létrehozása megbízható helyről való regisztráció megköveteléséhez

A következő házirend az összes kijelölt felhasználóra vonatkozik, akik a közös regisztrációs élmény használatával próbálnak regisztrálni, és blokkolja a hozzáférést, kivéve, ha egy megbízható hálózatként megjelölt helyről csatlakoznak.

1. A **Azure Portal**keresse meg **Azure Active Directory** > **biztonsági** > **feltételes hozzáférés**lehetőséget.
1. Válassza az **új szabályzat**lehetőséget.
1. A név mezőben adja meg a szabályzat nevét. Például **a megbízható hálózatokon található kombinált biztonsági adatok regisztrálása**.
1. A **hozzárendelések**területen kattintson a **felhasználók és csoportok**elemre, majd válassza ki azokat a felhasználókat és csoportokat, amelyekre alkalmazni szeretné a szabályzatot.

   > [!WARNING]
   > A felhasználók számára engedélyezni kell a [kombinált regisztráció előzetes](../authentication/howto-registration-mfa-sspr-combined.md)verzióját.

1. A **Cloud apps vagy műveletek**területen válassza a **felhasználói műveletek**lehetőséget, és jelölje be a **biztonsági adatok regisztrálása (előzetes verzió)** jelölőnégyzetet.
1. A **feltételek** > **helyen**.
   1. Konfigurálja az **Igen értéket**.
   1. Adjon meg **bármilyen helyet**.
   1. **Az összes megbízható helyszín**kizárása.
   1. Kattintson a **kész** gombra a helyszínek panelen.
   1. A feltételek panelen kattintson a **kész** gombra.
1. A **hozzáférés-vezérlés** > a **támogatás**elemre.
   1. Kattintson a **hozzáférés letiltása**elemre.
   1. Ezután kattintson a **Kiválasztás** elemre.
1. Állítsa a **Házirend engedélyezése** kapcsolót **Be** állásba.
1. Ezután kattintson a **Save** (Mentés) gombra.

## <a name="next-steps"></a>Következő lépések

[Feltételes hozzáférés – közös szabályzatok](concept-conditional-access-policy-common.md)

[A hatás meghatározása a feltételes hozzáférésről szóló jelentés módban](howto-conditional-access-report-only.md)

[Bejelentkezési viselkedés szimulálása a feltételes hozzáférési What If eszköz használatával](troubleshoot-conditional-access-what-if.md)
