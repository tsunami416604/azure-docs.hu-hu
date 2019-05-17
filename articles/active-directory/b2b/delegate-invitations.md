---
title: B2B-külső együttműködési beállítások – Azure Active Directory engedélyezése |} A Microsoft Docs
description: Ismerje meg, hogyan engedélyezheti a külső Active Directory B2B-együttműködés, és kezelni, akik vendégfelhasználókat is meghívhat. A Vendégmeghívó szerepkörrel használatával meghívók delegálása.
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
ms.openlocfilehash: 11dda7fc3760f468c094fb4cf4484a27895f83b9
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65812677"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>Külső B2B-együttműködés segítségével engedélyezheti és kezelheti, akik vendégek küldhetnek meghívót

Ez a cikk ismerteti, hogyan engedélyezheti az Azure Active Directory (Azure AD) B2B-együttműködés, és határozza meg, akik a vendégek küldhetnek meghívót. Alapértelmezés szerint minden felhasználó és a címtárban lévő vendégek is meghívhatnak vendégeket akkor is, ha azok még nem rendelt rendszergazda szerepkörrel. Külső együttműködési beállítások lehetővé teszik, hogy Vendég meghívók be- és kikapcsolhatja a különböző típusú felhasználók a szervezetben. Meghívók egyéni felhasználók számára is rendelhet a szerepköröket, amelyek lehetővé teszik számukra meghívhatnak vendégeket lehet delegálni.

## <a name="configure-b2b-external-collaboration-settings"></a>B2B külső együttműködési beállítások konfigurálása

Az Azure AD B2B együttműködés Bérlői rendszergazda állíthatja be a következő meghívó házirendek:

- Meghívók kikapcsolása
- Csak rendszergazdák és a Vendégmeghívó szerepkörű felhasználók küldhetnek meghívót
- A rendszergazdák, a Vendégmeghívó szerepkörrel és tagok küldhetnek meghívót
- Minden felhasználó,-Vendégek esetén is küldhetnek meghívót

Alapértelmezés szerint minden felhasználó, beleértve a vendégeket, vendégfelhasználókat is meghívhat.

### <a name="to-configure-external-collaboration-settings"></a>Külső együttműködési beállítások konfigurálása:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) bérlői rendszergazdaként.
2. Válassza ki **Azure Active Directory** > **felhasználók** > **felhasználói beállítások**.
3. A **külső felhasználók**válassza **külső együttműködési beállítások kezelése**.
   > [!NOTE]
   > A **külső együttműködési beállítások** is rendelkezésre állnak a **szervezeti kapcsolatok** lapot. Az Azure Active Directoryban a **kezelés**, lépjen a **szervezeti kapcsolatok** > **beállítások**.
4. Az a **külső együttműködési beállítások** lapon a engedélyezni szeretné a szabályzatok.

   ![Külső együttműködési beállítások](./media/delegate-invitations/control-who-to-invite.png)

  - **A vendégfelhasználók engedélyei korlátozottak**: Ez a szabályzat határozza meg a címtárban lévő vendégek engedélyeit. Válassza ki **Igen** bizonyos directory feladatokban, például felhasználók, csoportok vagy egyéb címtárerőforrások számbavételére számbavétele a blokk-vendégek számára. Válassza ki **nem** vendégek a ugyanazon hozzáférést adhat a címtáradatok normál felhasználóként a címtárban.
   - **Rendszergazdák és a vendégmeghívó szerepkörű felhasználók küldhetnek meghívót**: Ahhoz, hogy a rendszergazdák és felhasználók meghívhatnak vendégeket a "Vendég meghívója" szerepkörben, állítsa ezt a szabályzatot **Igen**.
   - **Tagok küldhetnek meghívót**: Ahhoz, hogy a nem rendszergazdai meghívhatnak vendégeket a címtár tagjai, állítsa ezt a szabályzatot **Igen**.
   - **Vendégek küldhetnek meghívót**: Ahhoz, hogy a vendégek meghívhatnak más vendégeket, állítsa ezt a szabályzatot **Igen**.
   - **E-mail egyszeri PIN-kód engedélyezése (előzetes verzió) vendégek**: A kapott egyszer használatos jelszót szolgáltatással kapcsolatos további információkért lásd: [e-mailben kapott egyszeri jelszót hitelesítés (előzetes verzió)](one-time-passcode.md).
   - **Együttműködési korlátozások**: Engedélyezi vagy blokkolja a Meghívók adott tartományokra kapcsolatos további információkért lásd: [engedélyezési és blokkolási Segítségkérések B2B-felhasználók az adott szervezetek](allow-deny-list.md).

## <a name="assign-the-guest-inviter-role-to-a-user"></a>A Vendégmeghívó szerepkör hozzárendelése felhasználóhoz

A Vendégmeghívó szerepkörrel rendelkező akkor is lehetővé teszik az egyes felhasználók meghívhatnak vendégeket egy globális rendszergazdai vagy egyéb rendszergazdai szerepkör hozzárendelése nélkül. A vendégmeghívó szerepkörű hozzárendelése egyéni felhasználók számára. Majd ellenőrizze, hogy a beállított **rendszergazdák és a vendégmeghívó szerepkörű felhasználók küldhetnek meghívót** való **Igen**.

A következő példa bemutatja, hogyan használhatja a Powershellt a felhasználó hozzáadása a Vendégmeghívó szerepkörrel:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>További lépések

Az Azure AD B2B együttműködés a következő cikkekben talál:

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
- [B2B együttműködés meghívó nélküli vendégfelhasználók hozzáadása](add-user-without-invite.md)
- [B2B-együttműködés felhasználó hozzáadása szerepkörhöz](add-guest-to-role.md)


