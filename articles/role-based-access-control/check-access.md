---
title: Rövid útmutató – Azure-erőforrások megtekintése a hozzáférés egy felhasználó rendelkezik |} A Microsoft Docs
description: Ismerje meg, a hozzáférés egy felhasználó vagy az egyéb rendszerbiztonsági tag van szerepköralapú hozzáférés-vezérlés (RBAC) és az Azure portal segítségével Azure-erőforrások megtekintése.
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
ms.openlocfilehash: f388215b2829066906ee7faf41abb17307bf3fff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60782325"
---
# <a name="quickstart-view-the-access-a-user-has-to-azure-resources"></a>Gyors útmutató: A felhasználó rendelkezik Azure-erőforrásokhoz való hozzáférés megtekintése

Használhatja a **hozzáférés-vezérlés (IAM)** paneljén [szerepköralapú hozzáférés-vezérlés (RBAC)](overview.md) a hozzáférés egy felhasználó, vagy a rendszerbiztonsági tag egy másik Azure-erőforrások megtekintéséhez. Azonban néha egyszerűen gyorsan megtekintheti az egy-egy felhasználóhoz vagy egy másik rendszerbiztonsági tagot a hozzáférést. Ennek legegyszerűbb módja az, hogy használja a **hozzáférés ellenőrzése** funkció az Azure Portalon.

## <a name="view-role-assignments"></a>Szerepkör-hozzárendelések megtekintése

 Az, hogy megtekintheti a hozzáférés egy felhasználó úgy, hogy a szerepkör-hozzárendelések lista. Kövesse az alábbi lépéseket a szerepkör-hozzárendeléseit egy egyetlen felhasználó, csoport, egyszerű szolgáltatás vagy az előfizetések szintjén felügyelt identitás megtekintéséhez.

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
> [Oktatóanyag: Egy felhasználó hozzáférést rbac-RÓL és az Azure portal segítségével Azure-erőforrások](quickstart-assign-role-user-portal.md)
