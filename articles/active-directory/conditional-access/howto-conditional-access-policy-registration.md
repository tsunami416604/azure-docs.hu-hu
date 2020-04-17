---
title: Feltételes hozzáférés – Kombinált biztonsági információk – Azure Active Directory
description: Egyéni feltételes hozzáférési házirend létrehozása a biztonsági adatok regisztrálásához
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
ms.openlocfilehash: 9c8081bb8145a6654c168fb2d664e1666b32dc18
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457909"
---
# <a name="conditional-access-securing-security-info-registration"></a>Feltételes hozzáférés: A biztonsági adatok regisztrációjának biztonságossá tétele

A feltételes hozzáférési szabályzat felhasználói lépéseivel biztosíthatja, hogy mikor és hogyan regisztrálhassanak a felhasználók az Azure többtényezős hitelesítésre és az önkiszolgáló jelszó-visszaállításra. Ez az előzetes verzió funkció azon szervezetek számára érhető el, amelyek engedélyezték a [kombinált regisztrációs előnézetet.](../authentication/concept-registration-mfa-sspr-combined.md) Ez a funkció engedélyezhető olyan szervezetekben, ahol olyan feltételeket szeretnének használni, mint a megbízható hálózati hely az Azure többtényezős hitelesítéshez való regisztrációhoz való hozzáférés korlátozásához és az önkiszolgáló jelszó-visszaállításhoz (SSPR). A használható feltételekről a Feltételes [hozzáférés: Feltételek](concept-conditional-access-conditions.md)című témakörben olvashat bővebben.

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Házirend létrehozása, amely megköveteli a regisztrációt egy megbízható helyről

A következő házirend minden kiválasztott felhasználóra vonatkozik, akik a kombinált regisztrációs felület használatával próbálnak regisztrálni, és letiltja a hozzáférést, kivéve, ha megbízható hálózatként megjelölt helyről csatlakoznak.

1. Az **Azure Portalon**keresse meg az Azure Active > **Directory–biztonsági** > **feltételes hozzáférést.** **Azure Active Directory**
1. Válassza az **Új házirend lehetőséget.**
1. A Név mezőbe írja be a házirend nevét. Például **a Kombinált biztonsági adatok regisztrálása megbízható hálózatokon**.
1. A **Hozzárendelések**csoportban válassza a **Felhasználók és csoportok**lehetőséget, és jelölje ki azokat a felhasználókat és csoportokat, amelyekre ez a házirend vonatkozni fog.

   > [!WARNING]
   > A felhasználókat engedélyezni kell a [kombinált regisztrációhoz.](../authentication/howto-registration-mfa-sspr-combined.md)

1. A **Felhőalapú alkalmazások vagy műveletek csoportban**válassza a **Felhasználói műveletek**lehetőséget, jelölje be a Biztonsági információk **regisztrálása**jelölőnégyzetet.
1. **Feltételek szerint** > **helyeken**.
   1. Állítsa be **az Igen**lehetőséget.
   1. **Tartalmazza a bármely helyet.**
   1. **Az összes megbízható hely kizárása**.
   1. Válassza a **Kész** lehetőséget a Helyek panelen.
   1. Válassza a **Kész** lehetőséget a Feltételek panelen.
1. A **Feltételek** > **az ügyfélalkalmazások (előzetes verzió)** csoportban állítsa a **Konfigurálás** beállítást **Igen**értékre, és válassza a **Kész gombot.**
1. A **Grant hozzáférési vezérlők csoportban.** > **Grant**
   1. Válassza **a Hozzáférés blokkolása**lehetőséget.
   1. Ezután kattintson a **Kiválasztás** elemre.
1. Állítsa a **Házirend engedélyezése** kapcsolót **Be** állásba.
1. Ezután válassza a **Save** (Mentés) lehetőséget.

Ebben a házirendben a 6. A fenti házirend megbízható hálózati helyről történő regisztrációt igényel. A szervezetek választhatnak, hogy a **helyszínek**helyett bármilyen rendelkezésre álló feltételt igénybe vehetnek. Ne feledje, hogy ez a házirend egy blokk házirend, így minden benne van tiltva, és bármi, ami nem felel meg a include megengedett. 

Néhányan dönthetnek úgy, hogy a fenti 6.

6. Az Eszköz **állapotának feltételei** > **(előzetes verzió)** alatt.
   1. Állítsa be **az Igen**lehetőséget.
   1. **Tartalmazza az összes eszközállapotát**.
   1. Az **eszközhibrid Azure AD-csatlakozás** és/vagy **megfelelőként megjelölt eszköz kizárása**
   1. Válassza a **Kész** lehetőséget a Helyek panelen.
   1. Válassza a **Kész** lehetőséget a Feltételek panelen.

> [!WARNING]
> Ha az eszközállapotot használja feltételként a házirendben, ez hatással lehet a címtárban lévő vendégfelhasználókra. [A csak jelentésmód](concept-conditional-access-report-only.md) segíthet a szakpolitikai döntések hatásának meghatározásában.

## <a name="next-steps"></a>További lépések

[Feltételes hozzáférés közös házirendjei](concept-conditional-access-policy-common.md)

[Hatás meghatározása csak feltételes hozzáférésű jelentésmódhasználatával](howto-conditional-access-report-only.md)

[Bejelentkezési viselkedés szimulálása a Feltételes hozzáférés Mi ha eszközzel](troubleshoot-conditional-access-what-if.md)
