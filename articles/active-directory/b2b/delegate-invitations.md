---
title: B2B külső együttműködési beállítások engedélyezése – Azure AD
description: Megtudhatja, hogy miként engedélyezheti az Active Directory B2B külső együttműködését, és hogyan kezelheti, hogy ki hívhat meg vendégfelhasználókat. Meghívók delegálása a Vendégmeghívó szerepkör használatával.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fb10863334392b207c7cfd2172dc9260cf15e2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272898"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>A B2B külső együttműködés engedélyezése és annak kezelése, hogy ki hívhat meg vendégeket

Ez a cikk ismerteti, hogyan engedélyezheti az Azure Active Directory (Azure AD) B2B együttműködést, és határozza meg, hogy ki hívhat vendégeket. Alapértelmezés szerint a címtárban lévő összes felhasználó és vendég akkor is meghívhat vendégeket, ha nincs enek rendszergazdai szerepkörhöz rendelve. A külső együttműködési beállítások lehetővé teszik a vendégmeghívók be- és kikapcsolását a szervezet különböző felhasználói számára. Az egyes felhasználóknak is delegálhat meghívókat, ha olyan szerepköröket rendel hozzá, amelyek lehetővé teszik számukra a vendégek meghívását.

## <a name="configure-b2b-external-collaboration-settings"></a>B2B külső együttműködési beállítások konfigurálása

Az Azure AD B2B együttműködéssel a bérlői rendszergazda a következő meghívási szabályzatokat állíthatja be:

- Meghívók kikapcsolása
- Csak a Vendégmeghívó szerepkör adminisztrátorai és felhasználói hívhatnak meg
- A rendszergazdák, a Vendégmeghívó szerepkör és a tagok meghívhatják
- Minden felhasználó, beleértve a vendégeket is,

Alapértelmezés szerint minden felhasználó, beleértve a vendégeket is, meghívhat vendégfelhasználókat.

### <a name="to-configure-external-collaboration-settings"></a>Külső együttműködési beállítások konfigurálása:

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) bérlői rendszergazdaként.
2. Válassza az **Azure Active Directory** > **felhasználóinak** > **beállításait.**
3. A **Külső felhasználók csoportban**válassza **a Külső együttműködési beállítások kezelése**lehetőséget.
   > [!NOTE]
   > A **Külső együttműködési beállítások** a Szervezeti kapcsolatok lapon is **elérhetők.** Az Azure Active Directory **Kezelése területén**nyissa meg a **Szervezeti kapcsolatok** > **beállításai t.**
4. A **Külső együttműködési beállítások** lapon válassza ki az engedélyezni kívánt házirendeket.

   ![Külső együttműködési beállítások](./media/delegate-invitations/control-who-to-invite.png)

  - **A vendégfelhasználók engedélyei korlátozottak:** Ez a házirend a címtárban szereplő vendégek engedélyeit határozza meg. Válassza az **Igen** lehetőséget, ha le szeretné tiltani a vendégeket bizonyos címtárfeladatokból, például a felhasználók, csoportok vagy más címtár-erőforrások számbavételétől. Válassza a **Nem** lehetőséget, ha a címtáradatokhoz ugyanolyan hozzáférést szeretne biztosítani a címtáradatokhoz, mint a címtárban lévő normál felhasználók.
   - **A vendégmeghívó szerepkör adminisztrátorai és felhasználói meghívhatják**a : Ha engedélyezni szeretné a "Vendégmeghívó" szerepkörben lévő rendszergazdáknak és felhasználóknak a vendégek meghívását, állítsa ezt a házirendet **Igen**beállításra.
   - **A tagok meghívhatják:** Ha engedélyezni szeretné a könyvtár nem rendszergazdai tagjainak a vendégek meghívását, állítsa ezt a házirendet **Igen**beállításra.
   - **A vendégek meghívhatják:** Ha engedélyezni szeretné a vendégeknek, hogy más vendégeket is meghívhassanak, állítsa ezt a szabályzatot **Igen**beállításra.
   - **Egyszeri e-mail-jelkód engedélyezése a vendégek számára (előzetes verzió):** Az egyszeri jelkódfunkcióról az [Egyszeri jelkód-hitelesítés küldése (előzetes verzió) című témakörben](one-time-passcode.md)talál további információt.
   - **Együttműködési korlátozások**: Az adott tartományokra szóló meghívók engedélyezéséről vagy letiltásáról az [Adott szervezetek b2b-felhasználóinak szóló meghívók engedélyezése vagy letiltása](allow-deny-list.md)című témakörben talál további információt.

## <a name="assign-the-guest-inviter-role-to-a-user"></a>A Vendégmeghívó szerepkör hozzárendelése felhasználóhoz

A Vendégmeghívó szerepkörrel lehetővé teheti az egyes felhasználók számára, hogy globális rendszergazdai vagy más rendszergazdai szerepkör hozzárendelése nélkül is meghívhassák őket. Rendelje hozzá a Vendég meghívó szerepkört az egyénekhez. Ezután győződjön meg arról, hogy a **rendszergazdák és a vendégmeghívó szerepkörben lévő felhasználók meghívhatják az** **Igen**lehetőséget.

Íme egy példa, amely bemutatja, hogyan használhatja a PowerShell t a vendégmeghívó szerepkörhöz való hozzáadásához:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>További lépések

Tekintse meg az alábbi cikkeket az Azure AD B2B együttműködés:

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
- [B2B együttműködési vendégfelhasználók hozzáadása meghívás nélkül](add-user-without-invite.md)
- [B2B együttműködési felhasználó hozzáadása szerepkörhöz](add-guest-to-role.md)


