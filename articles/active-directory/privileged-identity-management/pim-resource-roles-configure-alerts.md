---
title: Az Azure-erőforrások szerepköreihez tartozó biztonsági riasztások konfigurálása a PIM - Azure Active Directory |} A Microsoft Docs
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
ms.openlocfilehash: 09e04e6b61d3387cb8c50c2af4eef2cfb4bec196
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58575762"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-pim"></a>Az Azure-erőforrások szerepköreihez tartozó biztonsági riasztások konfigurálása az PIM-ben
Az Azure Active Directory (Azure AD) Privileged Identity Management (PIM) riasztásokat állít elő, ha bármi gyanúsat vagy nem biztonságos tevékenységre van a környezetben. Riasztást vált ki, amikor megjelenik a riasztások lapon. 

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
