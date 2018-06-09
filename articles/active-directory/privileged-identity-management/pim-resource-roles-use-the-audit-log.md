---
title: Erőforrás-szerepkörök az Azure-erőforrások naplózási Privileged Identity Management használatával |} Microsoft Docs
description: Ismerteti, hogyan láthatja az összes szerepkör tevékenység az egy adott erőforráshoz.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 211b8c69a1462f7efdcb4002269d96d1d5cf2ae6
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233784"
---
# <a name="audit-resource-roles-for-azure-resources-by-using-privileged-identity-management"></a>Naplózási erőforrás szerepkörök az Azure-erőforrások Privileged Identity Management használatával 

Erőforrás naplózási lehetővé teszi az összes szerepkör tevékenység az erőforrás nézetét. Az előre definiált dátum vagy egyéni tartomány információkat szűrheti is.
![Adatok](media/azure-pim-resource-rbac/rbac-resource-audit.png)

Erőforrás naplózási is a felhasználói tevékenység részletei gyors hozzáférést biztosít. A **naplózni típusát**, jelölje be **aktiválás**. Válassza ki **(tevékenységek)** az Azure-erőforrások, a felhasználó műveletek megjelenítéséhez.
![Tevékenység részletei](media/azure-pim-resource-rbac/rbac-audit-activity.png)

![További tevékenység részletei](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

# <a name="my-audit"></a>Saját naplózás

A naplózási lehetővé teszi a felhasználó személyes szerepkör tevékenységek nézetét. Az előre definiált dátum vagy egyéni tartomány információkat szűrheti is.
![Személyes szerepkör tevékenység](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="view-activation-and-azure-resource-activity"></a>Aktiválás és az Azure erőforrás-tevékenység megtekintése

Milyen műveletek egy adott felhasználó által a különböző erőforrások megtekintéséhez tekintse át az Azure-erőforrás tevékenység, amely a megadott aktiválási idővel rendelve van. A felhasználó megadásával indítsa el a **tagok** nézet vagy egy adott szerepkör tagjainak listája. Az eredmény megjelenítése a felhasználói műveletek az Azure-erőforrások dátum szerint. Adott azonos idő alatt a legutóbbi szerepkör aktiválások is látható.

![Felhasználói adatok](media/azure-pim-resource-rbac/rbac-user-details.png)

A szerepkör aktiválása részletek és a megfelelő Azure erőforrástevékenység történt, hogy a felhasználó active kiválasztása egy adott szerepkör aktiválása jeleníti meg.

![Válassza ki a szerepkör aktiválása](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

