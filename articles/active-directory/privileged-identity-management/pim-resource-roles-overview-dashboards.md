---
title: Erőforrás-irányítópultok a PIM hozzáférési felülvizsgálatához – Azure AD | Microsoft Docs
description: Ismerteti, hogyan használható az erőforrás-irányítópult a Azure AD Privileged Identity Management (PIM) hozzáférési felülvizsgálatának végrehajtásához.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6affa2ecc8919dabeb6173622b525280ce96bcfe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "73847024"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-privileged-identity-management"></a>Egy erőforrás-irányítópult használata hozzáférési felülvizsgálat elvégzésére Privileged Identity Management

Az erőforrás-irányítópult használatával hozzáférési felülvizsgálatot végezhet Privileged Identity Management (PIM) alkalmazásban. A Azure Active Directory (Azure AD) rendszergazdai nézetének irányítópultja három elsődleges összetevővel rendelkezik:

- Az erőforrás-szerepkörök aktiválásának grafikus ábrázolása
- A szerepkör-hozzárendelések hozzárendelési típus szerinti eloszlását megjelenítő diagramok
- Az új szerepkör-hozzárendelésekhez adatokat tartalmazó adatterület

![A felügyeleti nézet irányítópultjának képernyőképe, grafikonok és diagramok megjelenítése](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![A felügyeleti nézet irányítópultjának képernyőképe, amely megjeleníti az adatlistát](media/pim-resource-roles-overview-dashboards/role-settings.png)

Az erőforrás-szerepkörök aktiválásának grafikus ábrázolása az elmúlt hét napban terjed ki. Ezek az információk a kiválasztott erőforrásra vonatkoznak, és megjelenítik a leggyakoribb szerepkörök (tulajdonos, közreműködő, felhasználói hozzáférés rendszergazdája) és az összes szerepkör együttes aktiválásait.

Az aktiválási gráf egyik oldalán két diagram jeleníti meg a szerepkör-hozzárendelések hozzárendelési típus szerinti eloszlását mind a felhasználók, mind a csoportok esetében. A diagram egy szeletének kiválasztásával módosíthatja az értéket százalékra (vagy fordítva).

A diagramok alatt az új szerepkör-hozzárendeléssel rendelkező felhasználók és csoportok száma látható az elmúlt 30 napban, a szerepkörök pedig az összes hozzárendelés szerint csökkenő sorrendben rendezve jelennek meg.

## <a name="next-steps"></a>További lépések

- [Azure-beli erőforrás-szerepkörök hozzáférési felülvizsgálatának elindítása Privileged Identity Management](pim-resource-roles-start-access-review.md)
