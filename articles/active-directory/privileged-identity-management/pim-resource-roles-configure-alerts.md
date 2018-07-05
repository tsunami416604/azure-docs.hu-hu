---
title: Az Azure-erőforrások biztonsági riasztások kezelése a Privileged Identity Management használatával |} A Microsoft Docs
description: A PIM biztonsági riasztások ismerteti.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 9a773d5ae8ba07dc73cd2587039e2029ce50152d
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444999"
---
# <a name="manage-security-alerts-for-azure-resources-by-using-privileged-identity-management"></a>Az Azure-erőforrások biztonsági riasztások kezelése a Privileged Identity Management használatával
Privileged Identity Management (PIM) az Azure-erőforrások riasztásokat állít elő, ha bármi gyanúsat vagy nem biztonságos tevékenységre van a környezetben. Riasztást vált ki, amikor megjelenik a riasztások lapon. 

![Riasztások lap](media/azure-pim-resource-rbac/RBAC-alerts-home.png)

## <a name="review-alerts"></a>Riasztások áttekintése
Válasszon ki egy riasztást, a felhasználók vagy szerepkör, amely kiváltotta a riasztást, valamint szervizelési tanácsokat felsoroló jelentés megtekintéséhez.

![Riasztási jelentés](media/azure-pim-resource-rbac/rbac-alert-info.png)

## <a name="alerts"></a>Riasztások
| Riasztás | Severity | Eseményindító | Ajánlás |
| --- | --- | --- | --- |
| **Túl sok tulajdonos van hozzárendelve egy erőforráshoz** |Közepes |Túl sok felhasználót a tulajdonosi szerepkörrel rendelkezik. |Tekintse át a felhasználókat a listában, és néhány kisebb a kiemelt szerepkörökhöz való átrendelése. |
| **Túl sok állandó tulajdonos van hozzárendelve egy erőforráshoz** |Közepes |Túl sok felhasználó véglegesen egy szerepkörhöz vannak hozzárendelve. |Tekintse át a felhasználókat a listában, és rendelje hozzá néhányat a aktiválni kell szerepkört használni. |
| **Ismétlődő szerepkör létrehozása** |Közepes |Több szerepkör rendelkezik ugyanezeket a feltételeket. |Ezek a szerepkörök egyikét használhatja. |


### <a name="severity"></a>Severity
* **Magas**: azonnali beavatkozást igényel egy szabályzat megsértése miatt. 
* **Közepes**: nem igényelnek azonnali intézkedést, de lehetséges szabályzat megsértését jelzi.
* **Alacsony**: nem igényelnek azonnali intézkedést, de javasol a kívánt házirend módosítását.

## <a name="configure-security-alert-settings"></a>Biztonsági riasztási beállításainak konfigurálása
Lépjen a riasztások oldaláról **beállítások**.
![Beállítások](media/azure-pim-resource-rbac/rbac-navigate-settings.png)

A különböző riasztások beállításai a környezetet és biztonsági célok testreszabásához.
![A beállítások testre szabása](media/azure-pim-resource-rbac/rbac-alert-settings.png)
