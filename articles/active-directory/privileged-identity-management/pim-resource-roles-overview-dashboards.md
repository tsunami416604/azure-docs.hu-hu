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
ms.openlocfilehash: 37057e531ea8387fbed84c9b03bbfb9c14d160ea
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58574960"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-pim"></a>Hozzáférési felülvizsgálat végrehajtása a PIM erőforrás irányítópultok segítségével

Egy erőforrás Irányítópult segítségével hajtsa végre a hozzáférési felülvizsgálatok az Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Az adminisztrátori nézet irányítópult három elsődleges összetevőből áll:

- Szerepkör-aktiválások erőforrás grafikus ábrázolását.
- Két diagram, amely a szerepkör-hozzárendelések terjesztési hozzárendelés-típus szerinti megjelenítéséhez.
- Egy új szerepkör-hozzárendelések kérelmekre vonatkozó adatok területen.

![Képernyőkép az adminisztrátori nézet irányítópult gráfokat és diagramokat](media/azure-pim-resource-rbac/rbac-overview-top.png)

![Képernyőfelvétel a felügyelet nézet irányítópult adatok listája:](media/azure-pim-resource-rbac/role-settings.png)

A grafikus ábrázolását erőforrás szerepkör-aktiválások az elmúlt hét napban ismerteti. Ezeket az adatokat a kiválasztott erőforrás hatókörét, és megjeleníti a leggyakrabban használt szerepkörök (tulajdonos, közreműködő, felhasználói hozzáférés rendszergazdája), és az összes szerepköre kombinált aktiválások.

Az aktiválások graph jobbra két diagram megjelenítéséhez a szerepkör-hozzárendelések terjesztési hozzárendelési típust, felhasználókat és csoportokat is. Az érték a százalék (vagy fordítva), a diagram szelet kiválasztásával módosíthatja.

Alább a diagramok és a felhasználóknak és csoportoknak az új szerepkör-hozzárendelések száma az elmúlt 30 napban, valamint a szerepkörök (csökkenő sorrendben) hozzárendelések száma szerint rendezett listáját láthatja.

## <a name="next-steps"></a>További lépések

- [A PIM az Azure-erőforrások szerepköreihez tartozó hozzáférési felülvizsgálat indítása](pim-resource-roles-start-access-review.md) 
