---
title: Az Azure-erőforrások biztonsági riasztások kezelése a Privileged Identity Management használatával |} Microsoft Docs
description: A PIM biztonsági riasztások ismerteti.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 5d65201c8940e63e3ba514ae0a2dc8a247d7b859
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233512"
---
# <a name="manage-security-alerts-for-azure-resources-by-using-privileged-identity-management"></a>Az Azure-erőforrások biztonsági riasztások kezelése a Privileged Identity Management használatával
Privileged Identity Management (PIM) az Azure-erőforrások riasztásokat állít elő, ha nem biztonságos vagy gyanús tevékenységek a környezetben. Riasztást vált ki, ha azt mutatja a riasztások lapon. 

![Riasztások lap](media/azure-pim-resource-rbac/RBAC-alerts-home.png)

## <a name="review-alerts"></a>Riasztások áttekintése
Válasszon ki egy riasztást, a felhasználók vagy szerepkörök szervizelési tanácsot együtt, a riasztást kiváltó felsoroló jelentés megtekintéséhez.

![Riasztásjelentés](media/azure-pim-resource-rbac/rbac-alert-info.png)

## <a name="alerts"></a>Riasztások
| Riasztás | Severity | Eseményindító | Ajánlás |
| --- | --- | --- | --- |
| **Az erőforráshoz rendelt túl sok tulajdonosai** |Közepes |Túl sok felhasználó rendelkezik a tulajdonosi szerepkört. |Tekintse át a felhasználók a listán, és újbóli hozzárendelése a korlátozott engedélyekkel rendelkező szerepkörökhöz néhány. |
| **Az erőforráshoz rendelt túl sok állandó tulajdonosai** |Közepes |Túl sok felhasználó véglegesen egy szerepkörhöz vannak hozzárendelve. |Tekintse át a felhasználók a listán, és rendelje hozzá az egyes aktiválási szerepkör használata szükséges. |
| **Ismétlődő szerepkör létrehozása** |Közepes |Több szerepkör ugyanazok a feltételek rendelkezik. |Ezek a szerepkörök csak egyikét használhatja. |


### <a name="severity"></a>Severity
* **Magas**: azonnali beavatkozást igényel a szabályzat megsértése miatt. 
* **Közepes**: nem igényelnek azonnali beavatkozást, de egy lehetséges szabályzat megsértése jelzi.
* **Alacsony**: nem igényelnek azonnali beavatkozást, de a kívánt házirend módosítását javasol.

## <a name="configure-security-alert-settings"></a>Biztonsági riasztási beállításainak konfigurálása
Ugrás a riasztások lapon **beállítások**.
![Beállítások](media/azure-pim-resource-rbac/rbac-navigate-settings.png)

A környezet és a biztonsági célok szeretne használni a különböző riasztások beállítások testre szabhatók.
![A beállítások testre szabása](media/azure-pim-resource-rbac/rbac-alert-settings.png)
