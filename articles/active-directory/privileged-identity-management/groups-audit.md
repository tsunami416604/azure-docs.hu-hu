---
title: A rendszerjogosultságú hozzáférési csoport hozzárendeléseinek naplózási jelentésének megtekintése Privileged Identity Management (PIM) – Azure AD | Microsoft Docs
description: A Kiemelt jogosultságú hozzáférési csoport hozzárendeléseinek tevékenység-és naplózási előzményeinek megtekintése Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: pim
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9bbc90776ca007b84d5f67c50f8550ee9c881c7
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87506042"
---
# <a name="audit-activity-history-for-privileged-access-group-assignments-preview-in-privileged-identity-management"></a>A rendszerjogosultságú hozzáférési csoport hozzárendeléseinek (előzetes verzió) naplózása a Privileged Identity Management

A Privileged Identity Management (PIM) használatával megtekintheti az Azure privilegizált hozzáférési csoport tagjai és tulajdonosainak tevékenységeit, aktiválásait és naplózási előzményeit a Azure Active Directory (Azure AD) szervezeten belül.

> [!NOTE]
> Ha a szervezete egy Azure-beli [delegált erőforrás-kezelést](../../lighthouse/concepts/azure-delegated-resource-management.md)használó szolgáltatónál kiszervezett felügyeleti funkciókat használ, az adott szolgáltató által meghatalmazott szerepkör-hozzárendelések nem jelennek meg.

Az alábbi lépéseket követve megtekintheti a Kiemelt jogosultságú hozzáférési csoportok naplózási előzményeit.

## <a name="view-resource-audit-history"></a>Erőforrás-naplózási előzmények megtekintése

Az **erőforrás-naplózás** a Kiemelt hozzáférési csoportokhoz társított összes tevékenység áttekintését tartalmazza.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza a **privilegizált hozzáférés (előzetes verzió)** lehetőséget.

1. A **tevékenység**területen válassza az **erőforrás-naplózás**lehetőséget.

1. Az előzmények szűrése előre megadott dátummal vagy egyéni tartománnyal.

    ![Erőforrás-naplózási lista szűrőkkel](media/groups-audit/groups-resource-audit.png)

## <a name="view-my-audit"></a>Saját naplózás megtekintése

A saját audit lehetővé teszi a személyes szerepkör-tevékenységek megtekintését.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza a **privilegizált hozzáférés (előzetes verzió)** lehetőséget.

1. Válassza ki azt a tagot vagy csoportot, amelyre vonatkozóan meg szeretné tekinteni a naplózási előzményeket.

1. Válassza **a saját naplózás**lehetőséget.

1. Az előzmények szűrése előre megadott dátummal vagy egyéni tartománnyal.

    ![Az aktuális felhasználó naplózási listája](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>További lépések

- [Csoporttagság és tulajdonjog kiosztása Privileged Identity Management](groups-assign-member-owner.md)
- [Jogosultsági szintű hozzáférési csoportokra vonatkozó kérelmek jóváhagyása vagy elutasítása a PIM-ben](groups-approval-workflow.md)
- [Azure AD-szerepkörök naplózási előzményeinek megtekintése a PIM-ben](groups-audit.md)
