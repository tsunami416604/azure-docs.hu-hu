---
title: Rövid útmutató – a felhasználó hozzáférésének megtekintése az Azure-erőforrásokhoz – Azure RBAC
description: Ebből a rövid útmutatóból megtudhatja, hogyan tekintheti meg a felhasználók vagy más rendszerbiztonsági tag hozzáférését az Azure-erőforrásokhoz az Azure Portal és az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával.
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
ms.openlocfilehash: 9be53aa964e75bab0b90495640537fe927a5af0e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "82734161"
---
# <a name="quickstart-view-the-access-a-user-has-to-azure-resources"></a>Rövid útmutató: a felhasználó hozzáférésének megtekintése Azure-erőforrásokhoz

Az Azure [szerepköralapú hozzáférés-vezérlés (Azure RBAC)](overview.md) **hozzáférés-vezérlés (iam)** paneljén megtekintheti, hogy egy felhasználó vagy egy másik rendszerbiztonsági tag Hogyan férhet hozzá az Azure-erőforrásokhoz. Előfordulhat azonban, hogy csak egy adott felhasználó vagy egy másik rendszerbiztonsági tag hozzáférését kell gyorsan megtekintenie. Ennek a legegyszerűbb módja a Azure Portal **hozzáférés-ellenőrzési** funkciójának használata.

## <a name="view-role-assignments"></a>Szerepkör-hozzárendelések megtekintése

 A felhasználóhoz való hozzáférés megtekintésének módja a szerepkörök hozzárendeléseinek listázása. Az alábbi lépéseket követve megtekintheti egy adott felhasználó, csoport, szolgáltatásnév vagy felügyelt identitás szerepkör-hozzárendeléseit az előfizetés hatókörében.

1. A Azure Portal kattintson a **minden szolgáltatás** , majd az **előfizetések**elemre.

1. Kattintson az előfizetésre.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. Kattintson a **hozzáférés-ellenőrzési** lapra.

    ![Hozzáférés-vezérlés – hozzáférés ellenőrzése lap](./media/check-access/access-control-check-access.png)

1. A **Keresés** listában válassza ki, hogy milyen rendszerbiztonsági tag számára szeretné megtekinteni a hozzáférést.

1. A keresőmezőbe írja be a karakterláncot a megjelenítendő nevek, e-mail-címek vagy objektumazonosítók kereséséhez.

    ![Hozzáférés-kiválasztási lista kijelölése](./media/check-access/check-access-select.png)

1. Kattintson a rendszerbiztonsági tag elemre a **hozzárendelések** panel megnyitásához.

    ![hozzárendelések ablaktábla](./media/check-access/check-access-assignments.png)

    Ezen az ablaktáblán megtekintheti a kijelölt rendszerbiztonsági tag és a hatókörhöz rendelt szerepköröket. Ha ezen a hatókörön vannak megtagadási hozzárendelések, vagy a hatókör örökölt, a rendszer felsorolja őket.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: felhasználói hozzáférés biztosítása az Azure-erőforrásokhoz a Azure Portal használatával](quickstart-assign-role-user-portal.md)
