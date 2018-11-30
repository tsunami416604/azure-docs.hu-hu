---
title: Hozzáférés kezelése az RBAC és az Azure Portal használatával | Microsoft Docs
description: Ismerje meg, hogyan kezelheti a hozzáférést a felhasználók, csoportok, a szolgáltatásnevek és felügyelt identitások, a szerepköralapú hozzáférés-vezérlés (RBAC) és az Azure portal használatával. Ez tartalmazza a hozzáférés felsorolásának, a hozzáférés adásának és a hozzáférés eltávolításának módját.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9f5d48a074f8069e243af5644f86ad3c3d8f559b
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52634863"
---
# <a name="manage-access-using-rbac-and-the-azure-portal"></a>Hozzáférés kezelése az RBAC és az Azure Portal használatával

A [szerepköralapú hozzáférés-vezérlés (RBAC)](overview.md) az erőforrásokhoz való hozzáférés kezelésének a módja az Azure-ban. Ez a cikk bemutatja, hogyan kezelheti a felhasználók, csoportok, a szolgáltatásnevek és RBAC és az Azure portal használatával felügyelt identitások hozzáférését.

## <a name="open-access-control-iam"></a>Nyissa meg a hozzáférés-vezérlés (IAM)

A **hozzáférés-vezérlés (IAM)** panel, más néven identitás- és hozzáférés-kezelés alatt a portálon jelenik meg. Megtekintéséhez, vagy kezelheti a hozzáférést a portálon, a szokásos először is, nyissa meg a hatókörben, ahol szeretné megtekinteni, vagy módosítsa a hozzáférés-vezérlés (IAM) paneljén.

1. Az Azure Portalon kattintson a **minden szolgáltatás** , és válassza ki a hatókört vagy megtekinteni vagy kezelni kívánt erőforrást. Kiválaszthatja például **felügyeleti csoportok**, **előfizetések**, **erőforráscsoportok**, vagy egy erőforrást.

1. Kattintson az adott erőforrás szeretné megtekinteni vagy kezelni.

1. Kattintson a **hozzáférés-vezérlés (IAM)**.

    Az alábbiakban látható egy példa egy előfizetés hozzáférés-vezérlés (IAM) paneljén.

    ![Egy előfizetés hozzáférés-vezérlés (IAM) paneljén](./media/role-assignments-portal/access-control-subscription.png)

## <a name="view-roles-and-permissions"></a>Szerepkörök és engedélyek megtekintése

A szerepkör-definíció a szerepkör-hozzárendeléshez használható engedélyek gyűjteménye. Az Azure rendelkezik több mint 70 [beépített szerepkörök](built-in-roles.md). Kövesse az alábbi lépéseket a szerepköröket és engedélyeket, amelyek a felügyeleti síkra végrehajtható megtekintéséhez.

1. Nyissa meg **hozzáférés-vezérlés (IAM)** hatókörre, például a felügyeleti csoportban, előfizetés, erőforráscsoport vagy erőforrás, ahol szeretné megtekinteni a szerepköröket és engedélyeket.

1. Kattintson a **szerepkörök** lapján megtekintheti a beépített és egyéni szerepkörök listáját.

   Láthatja azon ebben a hatókörben található minden egyes szerepkörhöz rendelt felhasználók és csoportok számát.

   ![Szerepkörök listája](./media/role-assignments-portal/roles-list.png)

1. Kattintson egy egyéni szerepkör megtekintéséhez, aki ezt a szerepkört rendelték, és a szerepkör engedélyeit is megtekintheti.

   ![Szerepkör-hozzárendelések](./media/role-assignments-portal/role-assignments.png)

## <a name="view-role-assignments"></a>Szerepkör-hozzárendelések megtekintése

A hozzáférések kezelésekor szeretné tudni, kinek van hozzáférése, milyen engedélyekkel rendelkezik és milyen szinten. A lista hozzáférés egy felhasználó, csoport, szolgáltatásnevet vagy felügyelt identitást megtekintheti a szerepkör-hozzárendeléseket.

### <a name="view-role-assignments-for-a-single-user"></a>Szerepkör-hozzárendelések megtekintése egy-egy felhasználóhoz

Kövesse az alábbi lépéseket a hozzáférést egy egyetlen felhasználó, csoport, szolgáltatásnév vagy egy adott hatókörben felügyelt identitás megtekintéséhez.

1. Nyissa meg **hozzáférés-vezérlés (IAM)** hatókörre, például a felügyeleti csoportban, előfizetés, erőforráscsoport vagy erőforrás, ahol akarja tekinteni a hozzáférési.

1. Kattintson a **hozzáférés ellenőrzése** fülre.

    ![Hozzáférés - ellenőrzés hozzáférés lap](./media/role-assignments-portal/access-control-check-access.png)

1. Az a **található** listájához, válassza ki a hozzáférést az ellenőrizni kívánt rendszerbiztonsági tag típusát.

1. A keresőmezőbe adjon meg egy karakterláncot megfeleltessen keressen a könyvtárban, a megjelenítendő nevekre, e-mail-címek vagy az objektumazonosítók.

    ![Ellenőrizze a kiválasztási listán hozzáférés](./media/role-assignments-portal/check-access-select.png)

1. A rendszerbiztonsági tag megnyitásához kattintson a **hozzárendelések** ablaktáblán.

    ![hozzárendelések panelről](./media/role-assignments-portal/check-access-assignments.png)

    A panelen megjelenik a kiválasztott rendszerbiztonsági tag és a hatókör hozzárendelt szerepkörök. Ha bármelyik megtagadása a hatókör-hozzárendelést, vagy az örökölt ebben a hatókörben, azok megjelennek.

### <a name="view-all-role-assignments-at-a-scope"></a>A hatókör összes szerepkör-hozzárendelést megtekintése

1. Nyissa meg **hozzáférés-vezérlés (IAM)** hatókörre, például a felügyeleti csoportban, előfizetés, erőforráscsoport vagy erőforrás, ahol akarja tekinteni a hozzáférési.

1. Kattintson a **szerepkör-hozzárendelések** lap (vagy kattintson a **nézet** gombra a hozzárendelések csempére nézet szerepkör) ebben a hatókörben, szerepkör-hozzárendelések megtekintése.

   ![Hozzáférés-vezérlés – szerepkör-hozzárendeléseket lapon](./media/role-assignments-portal/access-control-role-assignments.png)

   A szerepkör-hozzárendelések lapon láthatja, kinek van hozzáférése a hatókörben. Látható, hogy egyes szerepkörök hatóköre **erre az erőforrásra** érvényes, míg mások más hatókörökből **(öröklődnek)**. Hozzáférés az erőforráshoz rendelt vagy a szülő hatókörnek egyik hozzárendeléséből örökölt.

## <a name="add-a-role-assignment"></a>Szerepkör-hozzárendelés hozzáadása

Az RBAC hozzáférést biztosítani, hogy szerepkör hozzárendelése egy felhasználó, csoport, szolgáltatásnevet vagy felügyelt identitás. Kövesse az alábbi lépéseket a különféle hatókörű hozzáférést.

### <a name="assign-a-role-at-a-scope"></a>Egy hatókörben szerepkör hozzárendelése

1. Nyissa meg **hozzáférés-vezérlés (IAM)** hatókörre, például a felügyeleti csoportban, előfizetés, erőforráscsoport vagy erőforrás, hol szeretne hozzáférést biztosítani.

1. Kattintson a **szerepkör-hozzárendelések** fülre kattintva megtekintheti a szerepkör-hozzárendeléseit a hatókörben.

1. Kattintson a **szerepkör-hozzárendelés hozzáadása** a hozzáadása szerepkör-hozzárendelés panel megnyitásához.

   Ha nem rendelkezik engedélyekkel szerepkörök hozzárendeléséhez, a Hozzáadás szerepkör-hozzárendelés beállítás letiltásra kerül.

   ![Szerepkör-hozzárendelési ablaktáblán hozzáadása](./media/role-assignments-portal/add-role-assignment.png)

1. A **Szerepkör** legördülő listájában válasszon ki egy szerepkört, például a **Virtuális gépek közreműködője** szerepkört.

1. Az a **kiválasztása** válasszon ki egy felhasználó, csoport, szolgáltatásnevet vagy felügyelt identitás. Ha a listában nem látja a rendszerbiztonsági tagot, írhat a **Kiválasztás** mezőbe megjelenítendő nevek, e-mail-címek és objektumazonosítók a címtárban történő kereséséhez.

1. Kattintson a **mentése** a szerepkör hozzárendelése.

   Néhány pillanat múlva a rendszerbiztonsági tag tartozik a szerepkör a kijelölt hatókörben.

### <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Az előfizetés rendszergazda felhasználó hozzárendelése

Ahhoz, hogy a felhasználó az Azure-előfizetés rendszergazdájának, hozzárendelheti azokat a [tulajdonosa](built-in-roles.md#owner) szerepkör az előfizetések szintjén. A tulajdonosi szerepkör a felhasználó teljes hozzáférést biztosít, az összes erőforrást az előfizetésben, beleértve a jogot arra, hogy mások való hozzáférés delegálására. Ezek a lépések ugyanazok, mint bármilyen más szerepkör-hozzárendelés.

1. Az Azure Portalon kattintson a **minden szolgáltatás** , majd **előfizetések**.

1. Kattintson arra az előfizetésre, ahol szeretné a hozzáférést.

1. Kattintson a **hozzáférés-vezérlés (IAM)**.

1. Kattintson a **szerepkör-hozzárendelések** fülre kattintva megtekintheti az előfizetéshez tartozó szerepkör-hozzárendelések.

1. Kattintson a **szerepkör-hozzárendelés hozzáadása** a hozzáadása szerepkör-hozzárendelés panel megnyitásához.

   Ha nem rendelkezik engedélyekkel szerepkörök hozzárendeléséhez, a Hozzáadás szerepkör-hozzárendelés beállítás letiltásra kerül.

   ![Szerepkör-hozzárendelési ablaktáblán hozzáadása](./media/role-assignments-portal/add-role-assignment.png)

1. Az a **szerepkör** legördülő listában válassza a **tulajdonosa** szerepkör.

1. Az a **kiválasztása** listában, válasszon ki egy felhasználót. Ha nem látja a felhasználónévre a listában, megadhatja a **kiválasztása** mezőben keressen a könyvtárban, a megjelenített nevek és e-mail-címeket.

1. Kattintson a **mentése** a szerepkör hozzárendelése.

   Néhány pillanat múlva a felhasználó hozzárendelve a tulajdonosi szerepkör az előfizetések szintjén.

## <a name="remove-role-assignments"></a>Szerepkör-hozzárendelések eltávolítása

Az RBAC-ben hozzáférés eltávolításához egy szerepkör-hozzárendelést kell eltávolítania. Kövesse az alábbi lépéseket a hozzáférést.

1. Nyissa meg **hozzáférés-vezérlés (IAM)** hatókörben egy, a felügyeleti csoport, előfizetés, erőforráscsoport vagy erőforrás, például, ahol el kívánja távolítani a hozzáférést.

1. Kattintson a **szerepkör-hozzárendelések** fülre kattintva megtekintheti az előfizetéshez tartozó szerepkör-hozzárendelések.

1. A szerepkör-hozzárendelések listájában jelölje be az eltávolítani kívánt szerepkör-hozzárendeléssel rendelkező rendszerbiztonsági tag melletti jelölőnégyzetet.

   ![Szerepkör-hozzárendelés eltávolítási üzenete](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Kattintson a **eltávolítása**.

   ![Szerepkör-hozzárendelés eltávolítási üzenete](./media/role-assignments-portal/remove-role-assignment.png)

1. A remove szerepkör hozzárendelése megjelenő üzenetben kattintson **Igen**.

    Az örökölt szerepkör-hozzárendeléseket nem lehet eltávolítani. Ha örökölt szerepkör-hozzárendelést kell eltávolítania, azt abban a hatókörben kell megtennie, ahol a szerepkör-hozzárendelés létrejött. Az a **hatókör** oszlopban, az **(örökölt)** egy hivatkozást, amellyel a hatókör, amelyekhez ezt a szerepkört hozzá volt rendelve. Lépjen az ott szereplő hatókörhöz a szerepkör-hozzárendelés eltávolításához.

   ![Szerepkör-hozzárendelés eltávolítási üzenete](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>További lépések

* [Oktatóanyag: Hozzáférést egy felhasználó RBAC és az Azure portal használatával](quickstart-assign-role-user-portal.md)
* [Oktatóanyag: Hozzáférés biztosítása egy felhasználó számára az RBAC és az Azure PowerShell használatával](tutorial-role-assignments-user-powershell.md)
* [Beépített szerepkörök](built-in-roles.md)
* [Az erőforrások rendszerezéséhez az Azure felügyeleti csoportok](../azure-resource-manager/management-groups-overview.md)
