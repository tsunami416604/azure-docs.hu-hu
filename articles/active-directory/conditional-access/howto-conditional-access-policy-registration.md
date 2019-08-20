---
title: Feltételes hozzáférés – kombinált biztonsági információk – Azure Active Directory
description: Egyéni feltételes hozzáférési szabályzat létrehozása a biztonsági adatok regisztrálásához szükséges megbízható hely megköveteléséhez
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
ms.openlocfilehash: f54ad6de21f05c76ca021e172a041563e3d688a8
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576562"
---
# <a name="conditional-access-require-trusted-location-for-mfa-registration"></a>Feltételes hozzáférés: Megbízható hely megkövetelése az MFA-regisztrációhoz

Az Azure multi-Factor Authentication és az önkiszolgáló jelszó-visszaállítás felhasználó általi regisztrálásának engedélyezése a felhasználók számára a feltételes hozzáférési házirend felhasználói műveleteivel. Ez az előzetes verziójú funkció olyan szervezetek számára érhető el, akik engedélyezték a [közös regisztráció előzetes](../authentication/concept-registration-mfa-sspr-combined.md)verzióját. Ez a funkció olyan szervezeteknél engedélyezhető, amelyekben a felhasználóknak regisztrálniuk kell az Azure multi-Factor Authentication szolgáltatásban, illetve a SSPR egy központi helyről, például egy megbízható hálózati helyről a HR bevezetése során. A megbízható helyek feltételes hozzáférésben való létrehozásával kapcsolatos további információkért tekintse meg a következő cikket: [Mi a hely feltétele a feltételes hozzáférés Azure Active Directory?](../conditional-access/location-condition.md#named-locations)

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Szabályzat létrehozása megbízható helyről való regisztráció megköveteléséhez

A következő házirend az összes kijelölt felhasználóra vonatkozik, akik a közös regisztrációs élmény használatával próbálnak regisztrálni, és blokkolja a hozzáférést, kivéve, ha egy megbízható hálózatként megjelölt helyről csatlakoznak.

1. A **Azure Portal**keresse meg **Azure Active Directory** > **feltételes hozzáférés**lehetőséget.
1. Válassza az **új szabályzat**lehetőséget.
1. A név mezőben adja meg a szabályzat nevét. Például **a megbízható hálózatokon található kombinált biztonsági adatok regisztrálása**.
1. A **hozzárendelések**területen kattintson a **felhasználók és csoportok**elemre, majd válassza ki azokat a felhasználókat és csoportokat, amelyekre alkalmazni szeretné a szabályzatot.

   > [!WARNING]
   > A felhasználók számára engedélyezni kell a [kombinált regisztráció előzetes](../authentication/howto-registration-mfa-sspr-combined.md)verzióját.

1. A **Cloud apps vagy műveletek**területen válassza a **felhasználói műveletek**lehetőséget, és jelölje be a **biztonsági adatok regisztrálása (előzetes verzió)** jelölőnégyzetet.
1. A **feltételek** > **helye**területen.
   1. Konfigurálja az **Igen értéket**.
   1. Adjon meg **bármilyen helyet**.
   1. **Az összes megbízható helyszín**kizárása.
   1. Kattintson a **kész** gombra a helyszínek panelen.
   1. A feltételek panelen kattintson a **kész** gombra.
1. A **hozzáférés-vezérlés** > **megadása**területen.
   1. Kattintson a **hozzáférés letiltása**elemre.
   1. Ezután kattintson a **Kiválasztás** elemre.
1. **Engedélyezze a házirend engedélyezése beállítást** **a**következőre:.
1. Ezt követően kattintson a **Create** (Létrehozás) gombra.

## <a name="next-steps"></a>További lépések

[Feltételes hozzáférés – közös szabályzatok](concept-conditional-access-policy-common.md)

[Bejelentkezési viselkedés szimulálása a feltételes hozzáférési What If eszköz használatával](troubleshoot-conditional-access-what-if.md)
