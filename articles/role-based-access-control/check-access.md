---
title: Rövid útmutató – A felhasználó Azure-erőforrásokhoz való hozzáférésének megtekintése
description: Ebben a rövid útmutatóban megtudhatja, hogyan tekintheti meg a hozzáférést egy felhasználó vagy más rendszerbiztonsági tag rendelkezik az Azure-erőforrások használatával szerepköralapú hozzáférés-vezérlés (RBAC) és az Azure Portalon.
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
ms.openlocfilehash: b23c10fc2a551b8044b208911dbc048968b06564
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "74419617"
---
# <a name="quickstart-view-the-access-a-user-has-to-azure-resources"></a>Rövid útmutató: A felhasználó Azure-erőforrásokhoz való hozzáférésének megtekintése

A [szerepköralapú hozzáférés-vezérlési (RBAC)](overview.md) **hozzáférés-vezérlési** panelen használhatja a felhasználó vagy más rendszerbiztonsági tag Azure-erőforrásokhoz való hozzáférésének megtekintéséhez. Néha azonban csak meg kell, hogy gyorsan tekintse meg a hozzáférést egy felhasználó vagy egy másik rendszerbiztonsági tag. Ennek legegyszerűbb módja a **Hozzáférés ellenőrzése** funkció használata az Azure Portalon.

## <a name="view-role-assignments"></a>Szerepkör-hozzárendelések megtekintése

 A felhasználó hozzáférésének megtekintéséhez úgy kell tekinteni, hogy felsorolhatja a szerepkör-hozzárendeléseit. Az alábbi lépésekkel megtekintheti egyetlen felhasználó, csoport, egyszerű szolgáltatás vagy felügyelt identitás szerepkör-hozzárendeléseit az előfizetéshatókörben.

1. Az Azure Portalon kattintson az **Összes szolgáltatás** elemre, majd **az Előfizetések**elemre.

1. Kattintson az előfizetésre.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. Kattintson a **Hozzáférés ellenőrzése** fülre.

    ![Hozzáférés-vezérlés – Hozzáférés lap ellenőrzése](./media/check-access/access-control-check-access.png)

1. A **Keresés** listában válassza ki azt a rendszerbiztonsági tag típusát, amelynek hozzáférését ellenőrizni szeretné.

1. A keresőmezőbe írjon be egy karakterláncot, amelyen megjelenítendő neveket, e-mail-címeket vagy objektumazonosítókat kereshet a könyvtárban.

    ![Hozzáférés kijelölési listájának ellenőrzése](./media/check-access/check-access-select.png)

1. Kattintson a rendszerbiztonsági tagra a **hozzárendelések** ablaktábla megnyitásához.

    ![hozzárendelések ablaktábla](./media/check-access/check-access-assignments.png)

    Ezen az ablaktáblán láthatja a kijelölt rendszerbiztonsági taghoz és a hatókörhöz rendelt szerepköröket. Ha van olyan megtagadási hozzárendelések ebben a hatókörben, vagy örökölt, hogy a hatókör, akkor a lista.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Felhasználói hozzáférés biztosítása az Azure-erőforrásokhoz az RBAC és az Azure Portal használatával](quickstart-assign-role-user-portal.md)
