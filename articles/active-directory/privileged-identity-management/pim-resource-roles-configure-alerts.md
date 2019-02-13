---
title: Az Azure-erőforrások szerepköreihez tartozó biztonsági riasztások konfigurálása a PIM |} A Microsoft Docs
description: Ismerje meg az Azure-erőforrások szerepköreihez tartozó biztonsági riasztások konfigurálása az Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 538661df85ca0960992cb40a53b0e149c3b566b6
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56189531"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-pim"></a>Az Azure-erőforrások szerepköreihez tartozó biztonsági riasztások konfigurálása az PIM-ben
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
* **Magas**: Egy szabályzat megsértése miatt azonnali beavatkozást igényel. 
* **Közepes**: Nem igényel azonnali intézkedést, de lehetséges szabályzat megsértését jelzi.
* **Alacsony**: Nem igényel azonnali intézkedést, de javasol a kívánt házirend módosítását.

## <a name="configure-security-alert-settings"></a>Biztonsági riasztási beállításainak konfigurálása
Lépjen a riasztások oldaláról **beállítások**.
![Beállítások](media/azure-pim-resource-rbac/rbac-navigate-settings.png)

A különböző riasztások beállításai a környezetet és biztonsági célok testreszabásához.
![A beállítások testre szabása](media/azure-pim-resource-rbac/rbac-alert-settings.png)

## <a name="next-steps"></a>További lépések

- [Az Azure-erőforrások szerepköreihez tartozó biztonsági riasztások konfigurálása az PIM-ben](pim-resource-roles-configure-alerts.md)
