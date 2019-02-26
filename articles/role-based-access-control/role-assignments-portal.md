---
title: Rbac-RÓL és az Azure portal segítségével Azure-erőforrásokhoz való hozzáférés kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan kezelheti a felhasználók, csoportok, a szolgáltatásnevek és szerepköralapú hozzáférés-vezérlés (RBAC) és az Azure portal használatával felügyelt identitások Azure-erőforrásokhoz való hozzáférését. Ez tartalmazza a hozzáférés felsorolásának, a hozzáférés adásának és a hozzáférés eltávolításának módját.
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
ms.date: 02/24/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: bb23cbc275e01eab5361504c547c020b0a29f4c3
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805290"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-azure-portal"></a>Rbac-RÓL és az Azure portal segítségével Azure-erőforrásokhoz való hozzáférés kezelése

[Szerepköralapú hozzáférés-vezérlés (RBAC)](overview.md) van az Azure-erőforrásokhoz való hozzáférés kezelése ugyanúgy. Ez a cikk bemutatja, hogyan kezelheti a hozzáférést az Azure portal használatával. Ha az Azure Active Directory-hozzáférés kezelése van szüksége, tekintse meg [nézet és rendelhet hozzá rendszergazdai szerepköröket az Azure Active Directoryban](../active-directory/users-groups-roles/directory-manage-roles-portal.md).

## <a name="prerequisites"></a>Előfeltételek

Adja hozzá, és távolítsa el a szerepkör-hozzárendeléseket, kell rendelkeznie:

- `Microsoft.Authorization/roleAssignments/write` és `Microsoft.Authorization/roleAssignments/delete` engedélyek, mint például [felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) vagy [tulajdonosa](built-in-roles.md#owner)

## <a name="overview-of-access-control-iam"></a>Hozzáférés-vezérlés (IAM) áttekintése

**Hozzáférés-vezérlés (IAM)** az a panel, amely az Azure-erőforrásokhoz való hozzáférés kezelésére. Más néven identitás és hozzáférés-kezelés és az Azure Portalon több helyen jelenik meg. Az alábbiakban látható egy példa egy előfizetés hozzáférés-vezérlés (IAM) paneljén.

![Egy előfizetés hozzáférés-vezérlés (IAM) paneljén](./media/role-assignments-portal/access-control-numbers.png)

Az alábbi táblázat azt ismerteti, milyen elemek közül néhány használja:

| # | Elem | Milyen használat esetében |
| --- | --- | --- |
| 1 | Ha hozzáférés-vezérlés (IAM) megnyitásakor erőforrás | Azonosítsa a hatókör (előfizetési ebben a példában) |
| 2 | **Adjon hozzá** gomb | Adja hozzá a szerepkör-hozzárendelések |
| 3 | **Hozzáférés ellenőrzése** lap | Egy felhasználó szerepkör-hozzárendelések megtekintése |
| 4 | **Szerepkör-hozzárendelések** lap | A szerepkör-hozzárendeléseket az aktuális hatókörben megtekintése |
| 5 | **Szerepkörök** lap | Szerepkörök és engedélyek megtekintése |

Ahhoz, hogy a hozzáférés-vezérlés (IAM) paneljén a leghatékonyabb, segít a Ha-hozzáférés kezelése során a következő három kérdésekre képes választ adni:

1. **Akiknek hozzáférésre van szüksége?**

    Akik hivatkozik egy felhasználó, csoport, szolgáltatásnevet vagy felügyelt identitás. Ez más néven egy *rendszerbiztonsági tag*.

1. **Milyen engedélyeket igényelnek?**

    Engedélyek vannak csoportosítva szerepkörökhöz. Számos beépített szerepkörök listájával, közül választhat.

1. **Amikor szükségük van hozzáférést?**

    Ahol erőforrásokat, amelyekre vonatkozik a hozzáférés hivatkozik. Hol lehet a felügyeleti csoport, előfizetés, erőforráscsoport vagy egyetlen erőforrás, például a storage-fiók. Ezt nevezzük a *hatókör*.

## <a name="open-access-control-iam"></a>Nyissa meg a hozzáférés-vezérlés (IAM)

Először is el kell döntenie, nyissa meg a hozzáférés-vezérlés (IAM) paneljén, hogy hol. Attól függ, milyen erőforrásokat, amelyeket a hozzáférés kezelésére. Biztosan való hozzáférés kezelése a felügyeleti csoportban, minden előfizetés, erőforráscsoport vagy egyetlen erőforrás-ban minden minden?

1. Az Azure Portalon kattintson a **minden szolgáltatás** , és válassza ki a hatókört. Kiválaszthatja például **felügyeleti csoportok**, **előfizetések**, **erőforráscsoportok**, vagy egy erőforrást.

1. Kattintson az adott erőforrás.

1. Kattintson a **hozzáférés-vezérlés (IAM)**.

    Az alábbiakban látható egy példa egy előfizetés hozzáférés-vezérlés (IAM) paneljén. Módosítások access control ide, ha azok a teljes előfizetés szeretné alkalmazni.

    ![Egy előfizetés hozzáférés-vezérlés (IAM) paneljén](./media/role-assignments-portal/access-control-subscription.png)

## <a name="view-roles-and-permissions"></a>Szerepkörök és engedélyek megtekintése

A szerepkör-definíció a szerepkör-hozzárendeléshez használható engedélyek gyűjteménye. Az Azure rendelkezik több mint 70 [beépített szerepkörök az Azure-erőforrások](built-in-roles.md). Kövesse az alábbi lépéseket az elérhető szerepkörök és engedélyek megtekintéséhez.

1. Nyissa meg **hozzáférés-vezérlés (IAM)** bármely hatókörben.

1. Kattintson a **szerepkörök** lapján megtekintheti a beépített és egyéni szerepkörök listáját.

   Láthatja, hogy a felhasználók és csoportok az aktuális hatókörben minden egyes szerepkörhöz rendelt számát.

   ![Szerepkörök listája](./media/role-assignments-portal/roles-list.png)

1. Kattintson egy egyéni szerepkör megtekintéséhez, aki ezt a szerepkört rendelték, és a szerepkör engedélyeit is megtekintheti.

   ![Szerepkör-hozzárendelések](./media/role-assignments-portal/role-assignments.png)

## <a name="view-role-assignments"></a>Szerepkör-hozzárendelések megtekintése

Ha hozzáférés-kezelés, érdemes figyelembe venni, kinek van hozzáférése, Mik azok az engedélyeiket, és milyen hatókörben. A lista hozzáférés egy felhasználó, csoport, szolgáltatásnevet vagy felügyelt identitást megtekintheti a szerepkör-hozzárendeléseket.

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

1. Kattintson a **szerepkör-hozzárendelések** fülre kattintva megtekintheti a szerepkör-hozzárendeléseit a hatókörben.

   ![Hozzáférés-vezérlés – szerepkör-hozzárendeléseket lapon](./media/role-assignments-portal/access-control-role-assignments.png)

   A szerepkör-hozzárendelések lapon láthatja, kinek van hozzáférése a hatókörben. Látható, hogy egyes szerepkörök hatóköre **erre az erőforrásra** érvényes, míg mások más hatókörökből **(öröklődnek)**. Hozzáférés az erőforráshoz rendelt vagy a szülő hatókörnek egyik hozzárendeléséből örökölt.

## <a name="add-a-role-assignment"></a>Szerepkör-hozzárendelés hozzáadása

Az RBAC hozzáférést biztosítani, hogy szerepkör hozzárendelése egy felhasználó, csoport, szolgáltatásnevet vagy felügyelt identitás. Kövesse az alábbi lépéseket a különféle hatókörű hozzáférést.

### <a name="assign-a-role-at-a-scope"></a>Egy hatókörben szerepkör hozzárendelése

1. Nyissa meg **hozzáférés-vezérlés (IAM)** hatókörre, például a felügyeleti csoportban, előfizetés, erőforráscsoport vagy erőforrás, hol szeretne hozzáférést biztosítani.

1. Kattintson a **szerepkör-hozzárendelések** fülre kattintva megtekintheti a szerepkör-hozzárendeléseit a hatókörben.

1. Kattintson a **Hozzáadás** > **szerepkör-hozzárendelés hozzáadása** a hozzáadása szerepkör-hozzárendelés panel megnyitásához.

   Ha nem rendelkezik engedélyekkel szerepkörök hozzárendeléséhez, a Hozzáadás szerepkör-hozzárendelés beállítás letiltásra kerül.

   ![Hozzáadásra szolgáló menü](./media/role-assignments-portal/add-menu.png)

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

1. Kattintson a **Hozzáadás** > **szerepkör-hozzárendelés hozzáadása** a hozzáadása szerepkör-hozzárendelés panel megnyitásához.

   Ha nem rendelkezik engedélyekkel szerepkörök hozzárendeléséhez, a Hozzáadás szerepkör-hozzárendelés beállítás letiltásra kerül.

   ![Hozzáadásra szolgáló menü](./media/role-assignments-portal/add-menu.png)

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

* [Oktatóanyag: Egy felhasználó hozzáférést rbac-RÓL és az Azure portal segítségével Azure-erőforrások](quickstart-assign-role-user-portal.md)
* [Oktatóanyag: Egy felhasználó hozzáférést biztosítani Azure-erőforrások RBAC és az Azure PowerShell használatával](tutorial-role-assignments-user-powershell.md)
* [Az RBAC hibaelhárítása az Azure-erőforrásokhoz](troubleshooting.md)
* [Az erőforrások rendszerezéséhez az Azure felügyeleti csoportok](../governance/management-groups/index.md)
