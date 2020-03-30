---
title: Az Azure-erőforrásszerepkörök naplózási jelentésének megtekintése a kiemelt identitáskezelés (PIM) – Azure AD | Microsoft dokumentumok
description: Az Azure-erőforrás-szerepkörök tevékenységének és naplózási előzményeinek megtekintése az Azure AD kiemelt identitáskezelés (PIM) szolgáltatásában.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bf65ad595fb1ab70eb6613b6d54ac2a4f69141e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78329604"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-privileged-identity-management"></a>Az Azure-erőforrás-szerepkörök tevékenységének és naplózási előzményeinek megtekintése a kiemelt identitáskezelésben

Az Azure Active Directory (Azure AD) privilegizált identitáskezelés (PIM) segítségével megtekintheti a tevékenységet, az aktiválásokat és a naplózási előzményeket a szervezeten belüli Azure-erőforrások szerepköreihez. Ez magában foglalja az előfizetések, erőforráscsoportok, és még a virtuális gépek. Az Azure Portalon belül az Azure szerepköralapú hozzáférés-vezérlési funkciókat használó bármely erőforrás kihasználhatja a kiemelt identitáskezelés biztonsági és életciklus-kezelési képességeit.

> [!NOTE]
> Ha a szervezet kiszervezte a felügyeleti funkciókat egy olyan szolgáltatóhoz, amely [az Azure delegált erőforrás-kezelését](../../lighthouse/concepts/azure-delegated-resource-management.md)használja, az adott szolgáltató által engedélyezett szerepkör-hozzárendelések itt nem jelennek meg.

## <a name="view-activity-and-activations"></a>Tevékenységek és aktiválások megtekintése

Ha meg szeretné tekinteni, hogy egy adott felhasználó milyen műveleteket végzett a különböző erőforrásokban, megtekintheti az Azure-erőforrás-tevékenységet, amely egy adott aktiválási időszakhoz van társítva.

1. Nyissa meg **az Azure AD kiemelt identitáskezelés .**

1. Válassza az **Azure-erőforrások lehetőséget.**

1. Válassza ki azt az erőforrást, amelynek a tevékenységét és aktiválásait meg szeretné tekinteni.

1. Válassza a **Szerepkörök** vagy **tagok lehetőséget.**

1. Jelöljön ki egy felhasználót.

    A felhasználó műveletek összegzését láthatja az Azure-erőforrásokban dátum szerint. Azt is mutatja, a legutóbbi szerepkör-aktiválások ugyanebben az időszakban.

    ![A felhasználók adatai az erőforrás-tevékenységek összegzésével és a szerepkör-aktiválásokkal](media/azure-pim-resource-rbac/rbac-user-details.png)

1. Válasszon ki egy adott szerepkör aktiválását a részletek és a megfelelő Azure-erőforrás-tevékenység megtekintéséhez, amely akkor történt, amikor a felhasználó aktív volt.

    [![Kiválasztott szerepkör-aktiválás és tevékenységrészletei](media/azure-pim-resource-rbac/export-membership.png "Kiválasztott szerepkör-aktiválás és tevékenységrészletei")](media/azure-pim-resource-rbac/export-membership.png)

## <a name="export-role-assignments-with-children"></a>Szerepkör-hozzárendelések exportálása gyermekekkel

Előfordulhat, hogy megfelelőségi követelmény, ahol meg kell adnia a teljes listát a szerepkör-hozzárendelések auditorok. A kiemelt identitáskezelés lehetővé teszi, hogy lekérdezze a szerepkör-hozzárendelések egy adott erőforrás, amely magában foglalja a szerepkör-hozzárendelések az összes gyermek erőforrás. Korábban a rendszergazdák nehezen kapták meg az előfizetés szerepkör-hozzárendeléseinek teljes listáját, és minden egyes erőforráshoz szerepkör-hozzárendeléseket kellett exportálniuk. A Kiemelt identitáskezelés használatával lekérdezheti az összes aktív és jogosult szerepkör-hozzárendelések egy előfizetésben, beleértve a szerepkör-hozzárendelések az összes erőforráscsoportok és erőforrások.

1. Nyissa meg **az Azure AD kiemelt identitáskezelés .**

1. Válassza az **Azure-erőforrások lehetőséget.**

1. Válassza ki azt az erőforrást, amelyhez szerepkör-hozzárendeléseket szeretne exportálni, például egy előfizetést.

1. Válassza ki a **Members** (Tagok) elemet.

1. Válassza **az Exportálás** lehetőséget a Tagság exportálása ablaktábla megnyitásához.

    [![Tagsági ablaktábla exportálása az összes tag exportálásához](media/azure-pim-resource-rbac/export-membership.png "Tagsági lap exportálása az összes tag exportálásához")](media/azure-pim-resource-rbac/export-membership.png)

1. Jelölje be **az Összes tag exportálása** jelölőnégyzetet a CSV-fájlösszes szerepkör-hozzárendelésének exportálásához.

    ![Exportált szerepkör-hozzárendelések a CSV-fájlban az Excel ben megjelenített ként](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>Erőforrás-naplózási előzmények megtekintése

Az erőforrás-naplózás az erőforrás összes szerepkör-tevékenységének nézetét biztosítja.

1. Nyissa meg **az Azure AD kiemelt identitáskezelés .**

1. Válassza az **Azure-erőforrások lehetőséget.**

1. Válassza ki azt az erőforrást, amelynek a naplózási előzményeit meg szeretné tekinteni.

1. Válassza **az Erőforrás-naplózás lehetőséget.**

1. Az előzmények szűrése előre meghatározott dátum vagy egyéni tartomány használatával.

    [![Erőforrás-naplózási lista szűrőkkel](media/azure-pim-resource-rbac/rbac-resource-audit.png "Erőforrás-naplózási lista szűrőkkel")](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. A **Naplózás típusban**válassza **az Aktiválás (Hozzárendelt + Aktivált)** lehetőséget.

    A ![ [![naplózási típus aktiválása szerint szűrt erőforrás-naplózási lista](media/azure-pim-resource-rbac/rbac-audit-activity.png "Az Aktiválás szerint szűrt erőforrás-naplózási lista")](media/azure-pim-resource-rbac/rbac-audit-activity.png) a naplózástípus aktiválása szerint szűrve](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. A **Művelet csoportban**kattintson **a (tevékenység)** elemre, ha meg szeretné tekinteni a felhasználó tevékenységének részleteit az Azure-erőforrásokban.

    ![Egy adott művelet felhasználói tevékenységének részletei](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>Saját naplózás megtekintése

Az auditom lehetővé teszi, hogy megtekinthesse személyes szerepkör-tevékenységét.

1. Nyissa meg **az Azure AD kiemelt identitáskezelés .**

1. Válassza az **Azure-erőforrások lehetőséget.**

1. Válassza ki azt az erőforrást, amelynek a naplózási előzményeit meg szeretné tekinteni.

1. Válassza **a Saját naplózás lehetőséget.**

1. Az előzmények szűrése előre meghatározott dátum vagy egyéni tartomány használatával.

    [![Az aktuális felhasználó naplózási listája](media/azure-pim-resource-rbac/my-audit-time.png "Az aktuális felhasználó naplózási listája")](media/azure-pim-resource-rbac/my-audit-time.png)

> [!NOTE]
> A naplózási előzményekhez való hozzáféréshez globális rendszergazdai vagy kiemelt szerep-rendszergazdai szerepkör szükséges.

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>Ok, jóváhagyó és jegyszám beszerezni a jóváhagyási eseményeket

1. Jelentkezzen be az [Azure Portalon](https://aad.portal.azure.com) kiemelt szerepkör-rendszergazdai szerepkör-engedélyekkel, és nyissa meg az Azure AD-t.
1. Válassza **a Naplók naplózása**lehetőséget.
1. A **Szolgáltatás** szűrő használatával csak a kiemelt identitáskezelési szolgáltatás naplózási eseményeit jelenítheti meg. A **Naplók naplózása** lapon a következőket teheti:

    - Tekintse meg a naplózási esemény okát az **Állapot oka** oszlopban.
    - Tekintse meg a jóváhagyó a **Kezdeményezte (aktor)** oszlopban a "Tag hozzáadása a szerepkörkérelemhez jóváhagyva" esemény.

    [![A PIM szolgáltatás naplójának szűrése](media/azure-pim-resource-rbac/filter-audit-logs.png "A PIM szolgáltatás naplójának szűrése")](media/azure-pim-resource-rbac/filter-audit-logs.png)

1. Válasszon ki egy naplóeseményt a jegyszám megtekintéséhez a **Részletek** ablaktábla **Tevékenység** lapján.
  
    [![Ellenőrizze a naplózási esemény jegyszámát](media/azure-pim-resource-rbac/audit-event-ticket-number.png "A naplózási esemény jegyszámának ellenőrzése")](media/azure-pim-resource-rbac/audit-event-ticket-number.png)]

1. A kérelmező (a szerepkört aktiváló személy) a **Részletek** ablaktábla **Részletek** lapján tekintheti meg a naplózási eseményt. Az Azure-erőforrás-szerepköröknek három céltípusa van:

    - A szerepkör (**típus** = szerepkör)
    - A kérelmező (**Típus** = Egyéb)
    - A jóváhagyó (**Típus** = Felhasználó)

    [![A céltípus ellenőrzése](media/azure-pim-resource-rbac/audit-event-target-type.png "A céltípus ellenőrzése")](media/azure-pim-resource-rbac/audit-event-target-type.png)

Általában a közvetlenül a jóváhagyási esemény feletti naplóesemény egy "Tag hozzáadása a szerepkörhöz befejeződött" esemény, ahol a **Initiated by (actor)** a kérelmező. A legtöbb esetben nem kell megtalálnia a kérelmezőt a jóváhagyási kérelemben egy naplózási szempontból.

## <a name="next-steps"></a>További lépések

- [Azure-erőforrás-szerepkörök hozzárendelése a kiemelt identitáskezelésben](pim-resource-roles-assign-roles.md)
- [Azure-erőforrás-szerepkörök kéréseinek jóváhagyása vagy megtagadása a kiemelt identitáskezelésben](pim-resource-roles-approval-workflow.md)
- [Az Azure AD-szerepkörök naplózási előzményeinek megtekintése a kiemelt identitáskezelésben](pim-how-to-use-audit-log.md)
