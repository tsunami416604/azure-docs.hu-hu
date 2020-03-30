---
title: Erőforrás-irányítópultok a PIM-ben végzett hozzáférés-felülvizsgálatokhoz – Azure AD | Microsoft dokumentumok
description: Bemutatja, hogyan használhatja az erőforrás-irányítópultot az Azure AD emelt szintű identitáskezelés (PIM) hozzáférési felülvizsgálatának végrehajtásához.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847024"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-privileged-identity-management"></a>Erőforrás-irányítópult használata hozzáférés-ellenőrzés végrehajtása a Kiemelt identitáskezelés ben

Egy erőforrás-irányítópult segítségével access review a kiemelt identitáskezelés (PIM) hozzáférési felülvizsgálat. Az Azure Active Directory (Azure AD) Felügyeleti nézet irányítópultja három fő összetevőből áll:

- Az erőforrásszerepkör-aktiválások grafikus ábrázolása
- A szerepkör-hozzárendelések hozzárendelés-típus szerint történő eloszlását megjelenítő diagramok
- Új szerepkör-hozzárendelések adatait tartalmazó adatterület

![Képernyőkép a Rendszergazdanézet irányítópultról, grafikonokkal és diagramokkal](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![Képernyőkép a Rendszergazdanézet irányítópultról, amelyen adatlisták láthatók](media/pim-resource-roles-overview-dashboards/role-settings.png)

Az erőforrásszerepkör-aktiválások grafikus ábrázolása az elmúlt hét napra vonatkozik. Ezek az adatok hatóköre a kijelölt erőforrás, és megjeleníti az aktiválások a leggyakoribb szerepkörök (tulajdonos, közreműködő, felhasználói hozzáférés rendszergazda) és az összes szerepkör együtt.

Az aktiválási diagram egyik oldalán két diagram jeleníti meg a szerepkör-hozzárendelések hozzárendelési típus szerint történő eloszlását mind a felhasználók, mind a csoportok esetében. Az értéket százalékra (vagy fordítva) módosíthatja, ha kijelöli a diagram egy szeletét.

A diagramok alatt az elmúlt 30 napban új szerepkör-hozzárendeléseket használó felhasználók és csoportok száma, valamint a teljes hozzárendelések szerint csökkenő sorrendben rendezett szerepkörök láthatók.

## <a name="next-steps"></a>További lépések

- [Access-ellenőrzés indítása az Azure-erőforrás-szerepkörökhöz a kiemelt identitáskezelésben](pim-resource-roles-start-access-review.md)
