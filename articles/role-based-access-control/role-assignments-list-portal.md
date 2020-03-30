---
title: Szerepkör-hozzárendelések listázása az Azure RBAC és az Azure Portal használatával
description: Megtudhatja, hogyan állapíthatja meg, hogy a felhasználók, csoportok, egyszerű szolgáltatás- és felügyelt identitások milyen erőforrásokhoz férhetnek hozzá az Azure szerepköralapú hozzáférés-vezérlés (RBAC) és az Azure Portal használatával.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 03a3d3c7d572d7ec5b8d3ac3d527d0d59e649bc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062248"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Szerepkör-hozzárendelések listázása az Azure RBAC és az Azure Portal használatával

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]Ez a cikk ismerteti, hogyan listázhatoa a szerepkör-hozzárendelések az Azure Portalhasználatával.

> [!NOTE]
> Ha a szervezet kiszervezte a felügyeleti funkciókat egy olyan szolgáltatóhoz, amely [az Azure delegált erőforrás-kezelését](../lighthouse/concepts/azure-delegated-resource-management.md)használja, az adott szolgáltató által engedélyezett szerepkör-hozzárendelések itt nem jelennek meg.

## <a name="list-role-assignments-for-a-user-or-group"></a>Felhasználó vagy csoport szerepkör-hozzárendelésének listázása

Az előfizetésben lévő felhasználóhoz vagy csoporthoz rendelt szerepkörök megtekintésének legegyszerűbb módja az **Azure-erőforrások** ablaktábla használata.

1. Az Azure Portalon kattintson a **Minden szolgáltatás** elemre, majd válassza a **Felhasználók** vagy **csoportok**lehetőséget.

1. Kattintson arra a felhasználóra vagy csoportra, amelyhez fel szeretné sorolni a szerepkör-hozzárendeléseket.

1. Kattintson **az Azure-erőforrások**elemre.

    A kijelölt felhasználóhoz vagy csoporthoz rendelt szerepkörök listája jelenik meg különböző hatókörökben, például felügyeleti csoportban, előfizetésben, erőforráscsoportban vagy erőforrásban. Ez a lista tartalmazza az összes olyan szerepkör-hozzárendelést, amelynek olvasási engedélye van.

    ![Felhasználó szerepkör-hozzárendelései](./media/role-assignments-list-portal/azure-resources-user.png)    

1. Az előfizetés módosításához kattintson az **Előfizetések listára.**

## <a name="list-owners-of-a-subscription"></a>Előfizetés tulajdonosainak listája

Azok a felhasználók, akik egy előfizetés [tulajdonosi](built-in-roles.md#owner) szerepkörrel rendelkeznek, kezelhetik az előfizetésben lévő összes et. Az alábbi lépésekkel listázva listázza az előfizetés tulajdonosait.

1. Az Azure Portalon kattintson az **Összes szolgáltatás** elemre, majd **az Előfizetések**elemre.

1. Kattintson arra az előfizetésre, amelynek tulajdonosait fel szeretné sorolni.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. Kattintson a **Szerepkör-hozzárendelések** lapra az előfizetés szerepkör-hozzárendeléseinek megtekintéséhez.

1. Görgessen a **Tulajdonosok** szakaszhoz az előfizetés tulajdonosi szerepköréhez rendelt összes felhasználó megtekintéséhez.

   ![Előfizetés-hozzáférés-vezérlés – Szerepkör-hozzárendelések lap](./media/role-assignments-list-portal/access-control-role-assignments-subscription.png)

## <a name="list-role-assignments-at-a-scope"></a>Szerepkör-hozzárendelések listázása hatókörben

1. Az Azure Portalon kattintson a **Minden szolgáltatás** elemre, majd válassza ki a hatókört. Kiválaszthatja például a **Felügyeleti csoportok**, **Az Előfizetések**, **az Erőforráscsoportok**vagy egy erőforrás lehetőséget.

1. Kattintson az adott erőforrásra.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. Kattintson a **Szerepkör-hozzárendelések** fülre a hatókör összes szerepkör-hozzárendelésének megtekintéséhez.

   ![Hozzáférés-vezérlés – Szerepkör-hozzárendelések lap](./media/role-assignments-list-portal/access-control-role-assignments.png)

   A Szerepkör-hozzárendelések lapon láthatja, hogy ki rendelkezik hozzáféréssel a hatókörhöz. Látható, hogy egyes szerepkörök hatóköre **erre az erőforrásra** érvényes, míg mások más hatókörökből **(öröklődnek)**. Az Access vagy kifejezetten ehhez az erőforráshoz van rendelve, vagy a szülőhatókörhöz rendelt hozzárendelésből öröklődik.

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>Felhasználó szerepkör-hozzárendelésének listázása hatókörben

Egy felhasználó, csoport, egyszerű szolgáltatás vagy felügyelt identitás elérésének listázásához sorolja fel a szerepkör-hozzárendeléseiket. Az alábbi lépésekkel listázható egyetlen felhasználó, csoport, egyszerű szolgáltatás vagy felügyelt identitás szerepkör-hozzárendelése egy adott hatókörben.

1. Az Azure Portalon kattintson a **Minden szolgáltatás** elemre, majd válassza ki a hatókört. Kiválaszthatja például a **Felügyeleti csoportok**, **Az Előfizetések**, **az Erőforráscsoportok**vagy egy erőforrás lehetőséget.

1. Kattintson az adott erőforrásra.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. Kattintson a **Hozzáférés ellenőrzése** fülre.

    ![Hozzáférés-vezérlés – Hozzáférés lap ellenőrzése](./media/role-assignments-list-portal/access-control-check-access.png)

1. A **Keresés** listában válassza ki azt a rendszerbiztonsági tag típusát, amelynek hozzáférését ellenőrizni szeretné.

1. A keresőmezőbe írjon be egy karakterláncot, amelyen megjelenítendő neveket, e-mail-címeket vagy objektumazonosítókat kereshet a könyvtárban.

    ![Hozzáférés kijelölési listájának ellenőrzése](./media/role-assignments-list-portal/check-access-select.png)

1. Kattintson a rendszerbiztonsági tagra a **hozzárendelések** ablaktábla megnyitásához.

    ![hozzárendelések ablaktábla](./media/role-assignments-list-portal/check-access-assignments.png)

    Ezen az ablaktáblán láthatja a kijelölt rendszerbiztonsági taghoz és a hatókörhöz rendelt szerepköröket. Ha van olyan megtagadási hozzárendelések ebben a hatókörben, vagy örökölt, hogy a hatókör, akkor a lista.

## <a name="list-role-assignments-for-a-system-assigned-managed-identity"></a>A rendszeráltal hozzárendelt felügyelt identitás szerepkör-hozzárendeléseinek listázása

1. Az Azure Portalon nyisson meg egy rendszer által hozzárendelt felügyelt identitást.

1. A bal oldali menüben kattintson az **Identitás parancsra.**

    ![Rendszerhez rendelt felügyelt identitás](./media/role-assignments-list-portal/identity-system-assigned.png)

1. A **Szerepkör-hozzárendelések csoportban**kattintson **a Felügyelt identitáshoz rendelt Azure RBAC-szerepkörök megjelenítése**elemre.

    Megjelenik a kiválasztott rendszeráltal hozzárendelt felügyelt identitáshoz rendelt szerepkörök listája különböző hatókörökben, például felügyeleti csoportban, előfizetésben, erőforráscsoportban vagy erőforrásban. Ez a lista tartalmazza az összes olyan szerepkör-hozzárendelést, amelynek olvasási engedélye van.

    ![Szerepkör-hozzárendelések a rendszer által hozzárendelt felügyelt identitáshoz](./media/role-assignments-list-portal/azure-resources-system-assigned.png)

## <a name="list-role-assignments-for-a-user-assigned-managed-identity"></a>Szerepkör-hozzárendelések listázása egy felhasználó által hozzárendelt felügyelt identitáshoz

1. Az Azure Portalon nyisson meg egy felhasználó által hozzárendelt felügyelt identitást.

1. Kattintson **az Azure-erőforrások**elemre.

    Megjelenik a kijelölt, felhasználó által hozzárendelt felügyelt identitáshoz rendelt szerepkörök listája különböző hatókörökben, például felügyeleti csoportban, előfizetésben, erőforráscsoportban vagy erőforrásban. Ez a lista tartalmazza az összes olyan szerepkör-hozzárendelést, amelynek olvasási engedélye van.

    ![Szerepkör-hozzárendelések a rendszer által hozzárendelt felügyelt identitáshoz](./media/role-assignments-list-portal/azure-resources-user-assigned.png)

1. Az előfizetés módosításához kattintson az **Előfizetések listára.**

## <a name="list-number-of-role-assignments"></a>Szerepkör-hozzárendelések listájának listája

Minden előfizetésben legfeljebb **2000** szerepkör-hozzárendelés lehet. A korlát nyomon követéséhez a **Szerepkör-hozzárendelések** lap tartalmaz egy diagramot, amely felsorolja az aktuális előfizetés szerepkör-hozzárendeléseinek számát.

![Hozzáférés-vezérlés – Szerepkör-hozzárendelések száma diagram](./media/role-assignments-list-portal/access-control-role-assignments-chart.png)

Ha közel van a maximális számhoz, és további szerepkör-hozzárendeléseket próbál hozzáadni, figyelmeztetés jelenik meg a **Szerepkör-hozzárendelés hozzáadása** ablaktáblán. A szerepkör-hozzárendelések számának csökkentésére az [Azure RBAC hibaelhárítása című témakörben](troubleshooting.md#azure-role-assignments-limit)van.

![Hozzáférés-vezérlés – Szerepkör-hozzárendelés hozzáadása – figyelmeztetés](./media/role-assignments-list-portal/add-role-assignment-warning.png)

## <a name="next-steps"></a>További lépések

- [Szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure RBAC és az Azure Portal használatával](role-assignments-portal.md)
- [Az RBAC hibáiaz Azure-erőforrásokhoz](troubleshooting.md)
