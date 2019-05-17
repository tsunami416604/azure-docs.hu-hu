---
title: Egy erőforrás Irányítópult segítségével hajtsa végre a PIM - Azure Active Directory hozzáférési felülvizsgálatok |} A Microsoft Docs
description: Ismerteti, hogyan lehet egy erőforrás Irányítópult segítségével a hozzáférési felülvizsgálat végrehajtása az Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5404d1b85821458aedef64b72ae635ea49aa1ff
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65602462"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-pim"></a>Hozzáférési felülvizsgálat végrehajtása a PIM erőforrás irányítópultok segítségével

Egy erőforrás Irányítópult segítségével hajtsa végre a hozzáférési felülvizsgálatok az Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Az adminisztrátori nézet irányítópult három elsődleges összetevőből áll:

- Szerepkör-aktiválások erőforrás grafikus ábrázolását.
- Két diagram, amely a szerepkör-hozzárendelések terjesztési hozzárendelés-típus szerinti megjelenítéséhez.
- Egy új szerepkör-hozzárendelések kérelmekre vonatkozó adatok területen.

![Képernyőkép az adminisztrátori nézet irányítópult gráfokat és diagramokat](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![Képernyőfelvétel a felügyelet nézet irányítópult adatok listája:](media/pim-resource-roles-overview-dashboards/role-settings.png)

A grafikus ábrázolását erőforrás szerepkör-aktiválások az elmúlt hét napban ismerteti. Ezeket az adatokat a kiválasztott erőforrás hatókörét, és megjeleníti a leggyakrabban használt szerepkörök (tulajdonos, közreműködő, felhasználói hozzáférés rendszergazdája), és az összes szerepköre kombinált aktiválások.

Az aktiválások graph jobbra két diagram megjelenítéséhez a szerepkör-hozzárendelések terjesztési hozzárendelési típust, felhasználókat és csoportokat is. Az érték a százalék (vagy fordítva), a diagram szelet kiválasztásával módosíthatja.

Alább a diagramok és a felhasználóknak és csoportoknak az új szerepkör-hozzárendelések száma az elmúlt 30 napban, valamint a szerepkörök (csökkenő sorrendben) hozzárendelések száma szerint rendezett listáját láthatja.

## <a name="next-steps"></a>További lépések

- [Azure-erőforrásszerepkörök hozzáférési felülvizsgálatának indítása a PIM szolgáltatásban](pim-resource-roles-start-access-review.md) 
