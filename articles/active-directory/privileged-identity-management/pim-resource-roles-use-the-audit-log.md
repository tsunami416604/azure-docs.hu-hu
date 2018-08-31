---
title: A PIM az Azure-erőforrások szerepköreihez tartozó naplózási előzmények megtekintése |} A Microsoft Docs
description: Ismerje meg az Azure-erőforrások szerepköreihez tartozó naplózási előzmények megtekintése az Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: c0536423e9640f78149b612ec66b0a07cdcf24bb
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189311"
---
# <a name="view-audit-history-for-azure-resource-roles-in-pim"></a>A PIM az Azure-erőforrások szerepköreihez tartozó naplózási előzmények megtekintése

Erőforrás naplózása megtekintheti az összes szerepkör-tevékenység az erőforrás. Szűrheti az adatokat egy előre definiált dátum- vagy egyéni tartomány használatával.
![Adatok](media/azure-pim-resource-rbac/rbac-resource-audit.png)

Erőforrás naplózása is egy felhasználói tevékenység részletei gyors hozzáférést biztosít. A **naplózás típusa**válassza **aktiválás**. Válassza ki **(tevékenységet)** annak a felhasználónak a műveletek az Azure-erőforrások megtekintéséhez.
![Tevékenység részletei](media/azure-pim-resource-rbac/rbac-audit-activity.png)

![Több tevékenység részletei](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="my-audit"></a>Saját naplózás

Saját naplózás megtekintheti a felhasználó személyes szerepkör tevékenység. Szűrheti az adatokat egy előre definiált dátum- vagy egyéni tartomány használatával.
![Személyes szerepkör tevékenység](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="view-activation-and-azure-resource-activity"></a>Aktiválás és az Azure-erőforrás tevékenység megtekintése

Milyen műveletek egy adott felhasználó tartott a különböző erőforrások megtekintéséhez áttekintheti az Azure-erőforrás tevékenység, amely egy adott aktiválási időszak van társítva. Első lépésként válassza ki a felhasználó a **tagok** nézet vagy egy adott szerepkör tagjai listájából. Az eredmény megjelenítése a felhasználói műveletek az Azure-erőforrások dátum szerint. Azt is bemutatja a legutóbbi szerepkör-aktiválások, hogy ugyanazon időszakra.

![Felhasználói adatok](media/azure-pim-resource-rbac/rbac-user-details.png)

Egy adott szerepkör-aktiválás kiválasztásával tekinthet meg a szerepkör aktiválásának részletei és a megfelelő Azure-erőforrás tevékenység, amely történt, miközben a felhasználó volt aktív.

![Válassza ki a szerepkör aktiválása](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

## <a name="next-steps"></a>További lépések

- [A PIM az Azure AD-címtárbeli szerepkörök naplózási előzmények megtekintése](pim-how-to-use-audit-log.md)
