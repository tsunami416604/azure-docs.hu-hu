---
title: B2B külső együttműködési beállítások engedélyezése – Azure AD
description: Megtudhatja, hogyan engedélyezheti Active Directory B2B külső együttműködését, és hogyan kezelheti a vendég felhasználókat meghívó személyeket. A meghívók delegálásához használja a vendég meghívó szerepkört.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f69069b0c62f1e96c16441e99fbadac45728743
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199430"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>Külső B2B-együttműködés engedélyezése és a vendégek meghívójának kezelése

Ez a cikk bemutatja, hogyan engedélyezheti a Azure Active Directory (Azure AD) B2B-együttműködést, és meghatározhatja, hogy kik hívhatják meg a vendégeket. Alapértelmezés szerint a címtárban lévő összes felhasználó és vendég meghívhatja a vendégeket, még akkor is, ha nincsenek rendszergazdai szerepkörhöz rendelve. A külső együttműködési beállítások lehetővé teszik a vendég meghívások be-és kikapcsolását a szervezet különböző típusaihoz. Az egyes felhasználóknak küldött meghívókat olyan szerepkörök hozzárendelésével is delegálhatja, amelyek lehetővé teszik a vendégek meghívását.

## <a name="configure-b2b-external-collaboration-settings"></a>B2B külső együttműködési beállítások konfigurálása

Az Azure AD B2B együttműködés révén a bérlői rendszergazdák a következő Meghívási szabályzatokat állíthatják be:

- Meghívók kikapcsolása
- Csak a vendég meghívó szerepkörben található rendszergazdák és felhasználók hívhatják meg
- Rendszergazdák, a vendég meghívó szerepkör, és a tagok meghívhatják
- Minden felhasználó, a vendégeket is meghívhatja

Alapértelmezés szerint az összes felhasználó, köztük a vendégek is meghívhatják a vendég felhasználókat.

### <a name="to-configure-external-collaboration-settings"></a>A külső együttműködési beállítások konfigurálása:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) bérlői rendszergazdaként.
2. Válassza a **Azure Active Directory**lehetőséget.
3. Válassza a **szervezeti kapcsolatok**  >  **beállításai** lehetőséget (vagy válassza ki a **külső identitások**  >  **külső együttműködési beállításokat**).
6. A **külső együttműködési beállítások** lapon válassza ki az engedélyezni kívánt házirendeket.

   ![Külső együttműködési beállítások](./media/delegate-invitations/control-who-to-invite.png)

  - A **vendég felhasználói engedélyei korlátozottak**: Ez a házirend határozza meg a címtárban lévő vendégek engedélyeit. Az **Igen** gombot választva letilthatja a vendégeket bizonyos címtárbeli feladatokból, például a felhasználók, csoportok vagy más címtárbeli erőforrások számbavételével. Válassza a **nem** lehetőséget, ha a címtárban lévő felhasználók számára ugyanazokat a hozzáférést szeretné biztosítani a felhasználóknak.
   - **Meghívhatják a rendszergazdákat és felhasználókat a vendég meghívó szerepkörben**: Ha engedélyezni szeretné a rendszergazdák és a felhasználók számára a "vendég meghívót" szerepkört a vendégek meghívásához, állítsa ezt a házirendet **Igen**értékre.
   - A **tagok meghívhatják**: ha lehetővé szeretné tenni a címtár nem rendszergazdai tagjai számára a vendégek meghívását, állítsa **Igen**értékre a szabályzatot.
   - A **vendégek meghívhatják**: ha lehetővé szeretné tenni a vendégek számára más vendégek meghívását, állítsa **Igen**értékre a szabályzatot.
   - **E-mail egyszeri jelszó engedélyezése a vendégek számára (előzetes verzió)**: az egyszeri PIN-kód szolgáltatással kapcsolatos további információkért lásd az [e-mailek egyszeri jelszavas hitelesítését (előzetes verzió)](one-time-passcode.md).
   - **Együttműködési korlátozások**: az adott tartományokra vonatkozó meghívások engedélyezésével vagy blokkolásával kapcsolatos további információkért lásd: a [vállalatközi felhasználók meghívásának engedélyezése vagy letiltása adott szervezetekben](allow-deny-list.md).

## <a name="assign-the-guest-inviter-role-to-a-user"></a>A vendég meghívó szerepkör társítása egy felhasználóhoz

A vendég meghívó szerepkörrel egyéni felhasználók is meghívhatják a vendégeket, anélkül, hogy globális rendszergazdai vagy egyéb rendszergazdai szerepkört rendeljenek hozzájuk. Rendelje hozzá a vendég meghívó szerepkört az egyéni felhasználókhoz. Ezután győződjön meg arról, hogy **a rendszergazdák és a felhasználók megadhatják a vendég meghívó szerepkört** **Igen**értékre.

Az alábbi példa bemutatja, hogyan adhat hozzá felhasználót a vendég meghívó szerepkörhöz a PowerShell használatával:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>További lépések

Tekintse meg a következő cikkeket az Azure AD B2B együttműködésről:

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
- [B2B Collaboration Guest Users hozzáadása meghívás nélkül](add-user-without-invite.md)
- [B2B csoportmunka-felhasználó hozzáadása egy szerepkörhöz](add-guest-to-role.md)


