---
title: Hagyja meg vendégként – Azure Active Directory-szervezet |} A Microsoft Docs
description: Bemutatja, hogyan egy Azure AD B2B vendégfelhasználó hagyhatja egy szervezet a hozzáférési panelen.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26d9eb883cc014c1bea092a12e22b6d144a37994
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112967"
---
# <a name="leave-an-organization-as-a-guest-user"></a>Egy szervezet meg vendégként

Egy Azure Active Directory (Azure AD) B2B vendégfelhasználó dönt, hogy egy szervezet bármikor hagyja, ha már nincs szükségük használják az alkalmazásokat, hogy a szervezet vagy karbantartása van társítva. A felhasználó hagyhatja egy szervezet saját, forduljon a rendszergazdához, nélkül.

> [!NOTE]
> Vendégfelhasználó egy szervezet nem hagyhatja, ha a fiók le van tiltva, a saját bérlőjén vagy az erőforrás-bérlő. Ha a fiók le van tiltva, a vendégfelhasználó kell lépjen kapcsolatba a bérlői rendszergazda, aki a Vendég fiók törlését, vagy hagyhatja, hogy a felhasználó a szervezetben, a Vendég fiók engedélyezve.

## <a name="leave-an-organization"></a>Szervezet elhagyása

Hogy egy szervezet, kövesse az alábbi lépéseket.

1. Nyissa meg a hozzáférési Panel profilja oldalát az alábbi lépések egyikének végrehajtásával:
   
   - Az a [az Azure portal](https://portal.azure.com), kattintson a jobb felső sarokban a nevére, és válassza ki **fiók megtekintése**.
   - Nyissa meg a [hozzáférési Panel](https://myapps.microsoft.com), kattintson a nevére, jobbra, és a Tovább gombra a felső **szervezetek**, válassza a beállítások (fogaskerék) ikonra.
 
   ![Képernyőfelvétel a hozzáférési panelen felhasználói beállításokat:](media/leave-the-organization/UserSettings.png) 

   > [!NOTE]
   > Ha Ön még nem tette a szervezet szeretné-e hagyni, a **szervezetek**, kattintson a **jelentkezzen be a szervezet** hivatkozást a szervezet neve mellett. Miután bejelentkezett, kattintson a nevére, ismét a jobb felső sarokban, és a **szervezetek**, válassza a beállítások (fogaskerék) ikonra.

3. A **szervezetek**, keresse meg a szervezet, amely hagyja, és válassza ki a kívánt **szervezet**.

   ![Képernyőfelvétel: a szervezet beállítást hagyja a felhasználói felületen](media/leave-the-organization/LeaveOrg.png)

4. Ha a rendszer kéri, erősítse meg, válassza ki a **hagyja**. 

## <a name="account-removal"></a>Fiók eltávolítása

Amikor egy felhasználó elhagyja a szervezetet, a felhasználói fiók "törölték" a címtárban. Alapértelmezés szerint a felhasználói objektum átkerül a **törölt felhasználók** terület az Azure ad-ben, de nem végleges törlése 30 napig. A helyreállítható törlés lehetővé teszi a rendszergazdák állítsa vissza a felhasználói fiók (beleértve a csoportok és engedélyek), ha a felhasználó kérést küld a fiók visszaállítása a 30 napos időszakon belül.

Igény szerint egy Bérlői rendszergazda véglegesen törölheti a fiókot a 30 napos időszakban bármikor. Ehhez tegye a következőket:

1. Az a [az Azure portal](https://portal.azure.com)válassza **Azure Active Directory**.
2. A **Kezelés** alatt válassza a **Felhasználókat**.
3. Válassza ki **törölt felhasználók**.
4. A törölt felhasználó melletti jelölőnégyzetet, majd válassza ki és **véglegesen törli a**.

Ha egy felhasználó véglegesen törli, ez a művelet nem módosítható.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>További lépések

- Az Azure AD B2B áttekintését lásd: [Mi az Azure AD B2B együttműködés?](what-is-b2b.md)



