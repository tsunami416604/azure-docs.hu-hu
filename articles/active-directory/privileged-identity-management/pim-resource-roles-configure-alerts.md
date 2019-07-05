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
ms.openlocfilehash: 3c7ce8b79644a9ffc9481ba825ec5623a9268983
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476333"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-pim"></a>Az Azure-erőforrások szerepköreihez tartozó biztonsági riasztások konfigurálása az PIM-ben
Az Azure Active Directory (Azure AD) Privileged Identity Management (PIM) riasztásokat állít elő, ha bármi gyanúsat vagy nem biztonságos tevékenységre van a környezetben. Riasztást vált ki, amikor megjelenik a riasztások lapon. 

![Azure-erőforrások – riasztások oldal száma, riasztás és kockázati szint listázása](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>Riasztások áttekintése
Válasszon ki egy riasztást, a felhasználók vagy szerepkör, amely kiváltotta a riasztást, valamint szervizelési tanácsokat felsoroló jelentés megtekintéséhez.

![Riasztási jelentés megjelenítése a legutóbbi vizsgálat idő, leírás, kárenyhítési lépések, típus, súlyosság, biztonsági kockázat, és hogyan lehet legközelebb megakadályozni](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

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

![Kiemelt beállításokkal a riasztások oldaláról](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

A különböző riasztások beállításai a környezetet és biztonsági célok testreszabásához.

![Lap engedélyezze és konfigurálja a beállításokat egy riasztás beállítása](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>További lépések

- [A PIM Azure szerepkör-beállítások konfigurálása](pim-resource-roles-configure-role-settings.md)
