---
title: Az Azure Resource szerepköreinek tevékenység-és naplózási előzményeinek megtekintése a PIM-Azure Active Directoryban | Microsoft Docs
description: Az Azure-erőforrás szerepköreinek tevékenység-és naplózási előzményeinek megtekintése Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40e1ed018f66d4f5eefd02d587504cf64969f47b
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804051"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-pim"></a>Az Azure-erőforrás szerepköreinek tevékenység-és naplózási előzményeinek megtekintése a PIM-ben

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) használatával megtekintheti a szervezeten belüli Azure-erőforrások szerepköreinek tevékenységeit, aktiválásait és naplózási előzményeit. Ide tartoznak az előfizetések, az erőforráscsoportok és a virtuális gépek is. A Azure Portalon belüli, az Azure szerepköralapú hozzáférés-vezérlést (RBAC) használó összes erőforrás kihasználhatja a PIM biztonsági és életciklus-kezelési funkcióit.

## <a name="view-activity-and-activations"></a>Tevékenységek és aktiválások megtekintése

Ha szeretné megtekinteni, hogy egy adott felhasználó milyen műveleteket végez a különböző erőforrásokban, megtekintheti az adott aktiválási időszakhoz társított Azure-erőforrás-tevékenységeket.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Kattintson az **Azure-erőforrások**elemre.

1. Kattintson arra az erőforrásra, amelynek a tevékenységeit és aktiválásait meg szeretné tekinteni.

1. Kattintson a **szerepkörök** vagy **tagok**elemre.

1. Kattintson a felhasználóra.

    A felhasználó az Azure-erőforrásokra vonatkozó műveleteinek dátum szerint grafikus nézetét jeleníti meg. Emellett az adott időszakra vonatkozó legutóbbi szerepkör-aktiválásokat is megjeleníti.

    ![Felhasználói adatok az erőforrás-tevékenység összefoglalásával és a szerepkör-aktiválásokkal](media/azure-pim-resource-rbac/rbac-user-details.png)

1. Egy adott szerepkör-aktiválásra kattintva megtekintheti a felhasználó aktív állapotban lévő adatait és a kapcsolódó Azure-erőforrás-tevékenységeket.

    ![Kiválasztott szerepkör-aktiválás és tevékenység részletei dátum szerint megjelenítve](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

## <a name="export-role-assignments-with-children"></a>Szerepkör-hozzárendelések exportálása gyermekekkel

Lehet, hogy megfelelőségi követelményt kell megadnia, amelyben meg kell adnia a rendszernaplók szerepkör-hozzárendeléseinek teljes listáját. A PIM lehetővé teszi a szerepkör-hozzárendelések lekérdezését egy adott erőforráson, amely tartalmazza az összes alárendelt erőforráshoz tartozó szerepkör-hozzárendeléseket. Korábban nehéz volt, hogy a rendszergazdák teljes listát kapjanak az előfizetéshez tartozó szerepkör-hozzárendelésekről, és minden egyes erőforráshoz hozzá kellett exportálni a szerepkör-hozzárendeléseket. A PIM használatával lekérdezheti az összes aktív és jogosult szerepkör-hozzárendelést egy előfizetésben, beleértve az összes erőforráscsoport és erőforrás szerepkör-hozzárendelését.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Kattintson az **Azure-erőforrások**elemre.

1. Kattintson arra az erőforrásra, amelyhez szerepkör-hozzárendeléseket szeretne exportálni, például egy előfizetést.

1. Kattintson a **tagok**elemre.

1. Kattintson az **Exportálás** elemre az Exportálás tagsága panel megnyitásához.

    ![Tagság exportálása ablaktábla az összes tag exportálásához](media/azure-pim-resource-rbac/export-membership.png)

1. Kattintson az **összes tag exportálása** lehetőségre egy CSV-fájlban lévő összes szerepkör-hozzárendelés exportálásához.

    ![Az exportált szerepkör-hozzárendelések a CSV-fájlban az Excelben való megjelenítéssel](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>Erőforrás-naplózási előzmények megtekintése

Az erőforrás-naplózás az erőforrás összes szerepkör-tevékenységének nézetét jeleníti meg.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Kattintson az **Azure-erőforrások**elemre.

1. Kattintson arra az erőforrásra, amelyre vonatkozóan meg szeretné tekinteni a naplózási előzményeket.

1. Kattintson az **erőforrás-naplózás**elemre.

1. Az előzmények szűrése előre megadott dátummal vagy egyéni tartománnyal.

    ![Erőforrás-naplózási lista szűrőkkel](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. A **naplózás típusa**beállításnál válassza az **aktiválás (hozzárendelt + aktivált)** lehetőséget.

    ![A naplózási típus aktiválásával szűrt erőforrás-naplózási lista](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. A **művelet**területen kattintson az **(tevékenység)** elemre a felhasználó számára, hogy megtekintse a felhasználó tevékenységének részleteit az Azure-erőforrásokban.

    ![Egy adott művelet felhasználói tevékenységének részletei](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>Saját naplózás megtekintése

A saját audit lehetővé teszi a személyes szerepkör-tevékenységek megtekintését.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Kattintson az **Azure-erőforrások**elemre.

1. Kattintson arra az erőforrásra, amelyre vonatkozóan meg szeretné tekinteni a naplózási előzményeket.

1. Kattintson **a saját naplózás**elemre.

1. Az előzmények szűrése előre megadott dátummal vagy egyéni tartománnyal.

    ![Az aktuális felhasználó naplózási listája](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>További lépések

- [Azure-beli erőforrás-szerepkörök kiosztása a PIM-ben](pim-resource-roles-assign-roles.md)
- [Azure-erőforrás-szerepkörökre vonatkozó kérelmek jóváhagyása vagy elutasítása a PIM-ben](pim-resource-roles-approval-workflow.md)
- [Azure AD-szerepkörök naplózási előzményeinek megtekintése a PIM-ben](pim-how-to-use-audit-log.md)
