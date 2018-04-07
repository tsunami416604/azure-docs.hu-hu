---
title: Az Azure-erőforrások – a biztonsági riasztások privileged Identity Management |} Microsoft Docs
description: A PIM biztonsági riasztások ismerteti.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 86c9a0f12b2598ffbd02810a11622b13b0363a1f
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---alerts"></a>Privileged Identity Management - szerepkör erőforrás - riasztások
Az Azure-erőforrások PIM riasztásokat állít elő, ha nem biztonságos vagy gyanús tevékenységek a környezetben. Riasztást vált ki, ha azt mutatja a riasztások lapon. 

![](media/azure-pim-resource-rbac/RBAC-alerts-home.png)

## <a name="review-alerts"></a>Riasztások áttekintése
Válasszon ki egy riasztást, a felhasználók vagy szerepkörök a riasztást kiváltó felsoroló jelentés megtekintéséhez valamint szervizelési a Tanácsot.
![](media/azure-pim-resource-rbac/rbac-alert-info.png)

## <a name="alerts"></a>Riasztások
| Riasztás | Súlyosság | Eseményindító | Ajánlás |
| --- | --- | --- | --- |
| **Az erőforráshoz rendelt túl sok tulajdonosai** |Közepes |Túl sok felhasználó rendelkezik a tulajdonosi szerepkört. |Tekintse át a felhasználók a listán, és rendelje hozzá az egyes korlátozott engedélyekkel rendelkező szerepkörökhöz. |
| **Az erőforráshoz rendelt túl sok állandó tulajdonosai** |Közepes |Túl sok felhasználó véglegesen egy szerepkörhöz vannak hozzárendelve. |Tekintse át a felhasználók a listán, és rendelje hozzá az egyes aktiválási szerepkör használata szükséges. |
| **Ismétlődő szerepkör létrehozása** |Közepes |Több szerepkör ugyanazok a feltételek rendelkezik. |Csak ezek a szerepkörök egyikét használhatja. |


### <a name="severity"></a>Súlyosság
* **Magas**: azonnali beavatkozást igényel a szabályzat megsértése miatt. 
* **Közepes**: nem igényelnek azonnali beavatkozást, de egy lehetséges szabályzat megsértése jelzi.
* **Alacsony**: nem igényelnek azonnali beavatkozást, de a preferrable házirend módosítását javasol.

## <a name="configure-security-alert-settings"></a>Biztonsági riasztási beállításainak konfigurálása
A riasztások lapon keresse meg a beállításokat.
![](media/azure-pim-resource-rbac/rbac-navigate-settings.png)

A környezet és a biztonsági célok szeretne használni a különböző riasztások beállítások testre szabhatók.
![](media/azure-pim-resource-rbac/rbac-alert-settings.png)
