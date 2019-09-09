---
title: Biztonsági riasztások konfigurálása Azure-beli erőforrás-szerepkörökhöz a PIM-ben – Azure Active Directory | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat biztonsági riasztásokat az Azure-erőforrás szerepköreihez a Azure AD Privileged Identity Management (PIM) szolgáltatásban.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/02/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 177f61392c3e441c891ba1b531301b3dae8c0db2
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804235"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-pim"></a>Biztonsági riasztások konfigurálása Azure-beli erőforrás-szerepkörökhöz a PIM-ben
A Azure Active Directory (Azure AD) Privileged Identity Management (PIM) riasztásokat állít elő, amikor gyanús vagy nem biztonságos tevékenység van a környezetben. Riasztások aktiválásakor megjelenik a riasztások lapon. 

![Azure-erőforrások – riasztások lap, a riasztás, a kockázati szint és a darabszám](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>Riasztások áttekintése
Válassza ki a riasztást, hogy megjelenjen egy jelentés, amely felsorolja a riasztást kiváltó felhasználókat vagy szerepköröket, valamint szervizelési tanácsokat.

![Riasztási jelentés, amely a legutóbbi vizsgálat idejét, a leírását, a kockázatcsökkentő lépéseket, a típust, a súlyosságot, a biztonsági hatásokat és a következő idő megelőzését mutatja be](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>Riasztások
| Riasztás | severity | Eseményindító | Ajánlás |
| --- | --- | --- | --- |
| **Túl sok tulajdonos van hozzárendelve egy erőforráshoz** |Közepes |Túl sok felhasználó rendelkezik tulajdonosi szerepkörrel. |Tekintse át a listában szereplő felhasználókat, és rendeljen hozzá néhányat a kevésbé Kiemelt szerepkörökhöz. |
| **Túl sok állandó tulajdonos van hozzárendelve egy erőforráshoz** |Közepes |Túl sok felhasználó van hozzá véglegesen rendelve egy szerepkörhöz. |Tekintse át a listában szereplő felhasználókat, és rendelje újra a szerepkör-használathoz szükséges aktiválást. |
| **Ismétlődő szerepkör létrehozva** |Közepes |Több szerepkör azonos feltételekkel rendelkezik. |Csak az egyik szerepkört használja. |


### <a name="severity"></a>severity
* **Magas**: A szabályzat megsértése miatt azonnali műveletre van szükség. 
* **Közepes**: Nincs szükség azonnali műveletre, de lehetséges szabályzat megsértését jelzi.
* **Alacsony**: Nincs szükség azonnali műveletre, de javasolt házirend-módosítást javasol.

## <a name="configure-security-alert-settings"></a>Biztonsági riasztás beállításainak konfigurálása
A riasztások lapon lépjen a **Beállítások**elemre.

![Riasztások lap Kiemelt beállításokkal](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

A különböző riasztások beállításainak testreszabásával dolgozhat a környezettel és a biztonsági célokkal.

![Riasztás beállítása a beállítások engedélyezéséhez és konfigurálásához](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>További lépések

- [Az Azure erőforrás-szerepkör beállításainak konfigurálása a PIM-ben](pim-resource-roles-configure-role-settings.md)
