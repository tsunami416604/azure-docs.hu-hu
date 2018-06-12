---
title: 'Áttekintés: A Privileged Identity Management egy hozzáférési felülvizsgálat végrehajtása az Azure-erőforrások |} Microsoft Docs'
description: Ez a dokumentum ismerteti, hogyan hajthat végre egy áttekintése a PIM, az Azure-erőforrások.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: protection
ms.date: 03/30/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 2f96f9c70d6a12aeddd51602bccd7f03f89ec7d1
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35260868"
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


