---
title: Erőforrás-irányítópult használata hozzáférési felülvizsgálat végrehajtásához a PIM-Azure Active Directoryban | Microsoft Docs
description: Ismerteti, hogyan használható az erőforrás-irányítópult a Azure AD Privileged Identity Management (PIM) hozzáférési felülvizsgálatának végrehajtásához.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 03/30/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e759ba47c16617aa1783ce6fb0e324aa62ee96d
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804111"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-pim"></a>Erőforrás-irányítópult használata hozzáférési felülvizsgálat végrehajtására a PIM-ben

Az erőforrás-irányítópult használatával hozzáférési felülvizsgálatot végezhet Azure Active Directory (Azure AD) Privileged Identity Management (PIM) szolgáltatásban. A felügyeleti nézet irányítópultján három elsődleges összetevő van:

- Az erőforrás-szerepkörök aktiválásának grafikus ábrázolása.
- Két olyan diagram, amely a szerepkör-hozzárendelések hozzárendelési típus szerinti eloszlását jeleníti meg.
- Az új szerepkör-hozzárendelésekhez tartozó adatterület.

![A felügyeleti nézet irányítópultjának képernyőképe, grafikonok és diagramok megjelenítése](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![A felügyeleti nézet irányítópultjának képernyőképe, amely megjeleníti az adatlistát](media/pim-resource-roles-overview-dashboards/role-settings.png)

Az erőforrás-szerepkörök aktiválásának grafikus ábrázolása az elmúlt hét napban terjed ki. Ezek az információk a kiválasztott erőforrásra vonatkoznak, és megjelenítik a leggyakoribb szerepkörök (tulajdonos, közreműködő, felhasználói hozzáférés rendszergazdája) és az összes szerepkör együttes aktiválásait.

Az aktiválási gráftól jobbra két diagram jeleníti meg a szerepkör-hozzárendelések hozzárendelési típus szerinti eloszlását mind a felhasználók, mind a csoportok esetében. A diagram egy szeletének kiválasztásával módosíthatja az értéket százalékra (vagy fordítva).

A diagramok alatt az új szerepkör-hozzárendelésekkel rendelkező felhasználók és csoportok száma látható az elmúlt 30 napban, valamint a szerepkörök listáját a teljes hozzárendelések szerint rendezve (csökkenő sorrendben).

## <a name="next-steps"></a>További lépések

- [Azure-erőforrásszerepkörök hozzáférési felülvizsgálatának indítása a PIM szolgáltatásban](pim-resource-roles-start-access-review.md) 
