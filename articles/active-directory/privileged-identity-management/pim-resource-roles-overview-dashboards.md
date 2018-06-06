---
title: 'Áttekintés: A Privileged Identity Management egy hozzáférési felülvizsgálat végrehajtása az Azure-erőforrások |} Microsoft Docs'
description: Ez a dokumentum ismerteti, hogyan hajthat végre egy áttekintése a PIM, az Azure-erőforrások.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 8ce8ca4cec9852c4453f79aabe84763ae926eaef
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34699709"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review"></a>Egy erőforrás Irányítópult segítségével hajtsa végre egy áttekintése

Egy erőforrás-Irányítópult segítségével Privileged Identity Management (PIM) egy hozzáférési felülvizsgálat végrehajtása az Azure-erőforrások. A felügyelet nézet irányítópult három elsődleges összetevőből áll:

- Erőforrás szerepkör aktiválások grafikus ábrázolása.
- A szerepkör-hozzárendelések terjesztési megjeleníthető hozzárendelés-típus két diagramot.
- Egy új szerepkör-hozzárendelések vonatkozó adatok területen.

![Képernyőfelvétel: a felügyelet nézet irányítópult diagramjait és diagramokat](media/azure-pim-resource-rbac/rbac-overview-top.png)

![Képernyőfelvétel: a felügyelet nézet irányítópult adatok listája](media/azure-pim-resource-rbac/role-settings.png)

Az erőforrás szerepkör aktiválások grafikus ábrázolása hozzá van rendelve az elmúlt hét napban. Az adatok hatóköre a kiválasztott erőforrás a, és a leggyakrabban használt szerepkörök (tulajdonos, közreműködő, felhasználói hozzáférés adminisztrátora), valamint az összes szerepkör kombinált aktiválások jeleníti meg.

Jobb oldalán a aktiválások grafikon két diagramok megjelenítéséhez szerepkör-hozzárendelések terjesztési hozzárendelés-típus, a felhasználókat és csoportokat egyaránt. Az érték a százalék (vagy fordítva), a diagram szelet kiválasztásával módosíthatja.

Alább a diagramok a felhasználókat és csoportokat az új szerepkör-hozzárendelések száma az elmúlt 30 napban, és a teljes hozzárendelések (csökkenő sorrendben) szerint rendezve szerepkörök listáját láthatja.


