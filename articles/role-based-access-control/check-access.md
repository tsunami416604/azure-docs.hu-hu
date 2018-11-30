---
title: Rövid útmutató – az Azure Portalon a felhasználóhoz rendelt szerepköreinek megtekintése |} A Microsoft Docs
description: Ismerje meg, hogyan rendelve egy felhasználó, csoport, egyszerű szolgáltatás vagy az Azure portal használatával felügyelt identitás szerepköralapú hozzáférés-vezérlés (RBAC) vonatkozó engedélyeket.
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 11/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b755dd6223c21084cafea82a1c8857f9f54b03b5
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52638652"
---
# <a name="quickstart-view-roles-assigned-to-a-user-using-the-azure-portal"></a>Gyors útmutató: Az Azure portal használatával egy felhasználóhoz hozzárendelt szerepkörök megtekintése

Használhatja a **hozzáférés-vezérlés (IAM)** paneljén [szerepköralapú hozzáférés-vezérlés (RBAC)](overview.md) megtekintéséhez a szerepkör-hozzárendeléseket több felhasználó csoportokra vonatkoznak, az egyszerű szolgáltatások, és a felügyelt identitásokból, de néha gyorsan megtekintheti a szerepkör-hozzárendeléseit egy egyetlen felhasználó, csoport, szolgáltatásnevet vagy felügyelt identitás csak kell. Ennek legegyszerűbb módja az, hogy használja a **hozzáférés ellenőrzése** funkció az Azure Portalon.

## <a name="view-role-assignments"></a>Szerepkör-hozzárendelések megtekintése

Kövesse az alábbi lépéseket a szerepkör-hozzárendeléseit egy egyetlen felhasználó, csoport, egyszerű szolgáltatás vagy az előfizetések szintjén felügyelt identitás megtekintéséhez.

1. Az Azure Portalon kattintson a **minden szolgáltatás** , majd **előfizetések**.

1. Kattintson az előfizetésre.

1. Kattintson a **hozzáférés-vezérlés (IAM)**.

1. Kattintson a **hozzáférés ellenőrzése** fülre.

    ![Hozzáférés - ellenőrzés hozzáférés lap](./media/check-access/access-control-check-access.png)

1. Az a **található** listájához, válassza ki a hozzáférést az ellenőrizni kívánt rendszerbiztonsági tag típusát.

1. A keresőmezőbe adjon meg egy karakterláncot megfeleltessen keressen a könyvtárban, a megjelenítendő nevekre, e-mail-címek vagy az objektumazonosítók.

    ![Ellenőrizze a kiválasztási listán hozzáférés](./media/check-access/check-access-select.png)

1. A rendszerbiztonsági tag megnyitásához kattintson a **hozzárendelések** ablaktáblán.

    ![hozzárendelések panelről](./media/check-access/check-access-assignments.png)

    A panelen megjelenik a kiválasztott rendszerbiztonsági tag és a hatókör hozzárendelt szerepkörök. Ha bármelyik megtagadása a hatókör-hozzárendelést, vagy az örökölt ebben a hatókörben, azok megjelennek.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Hozzáférést egy felhasználó RBAC és az Azure portal használatával](quickstart-assign-role-user-portal.md)
