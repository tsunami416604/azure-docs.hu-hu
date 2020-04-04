---
title: Erőforráscsoportok kezelése – Azure portal
description: Az Azure Portal használatával kezelheti erőforráscsoportjait az Azure Resource Manager en keresztül. Erőforráscsoportok létrehozása, listázása és törlése.
author: mumian
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: jgao
ms.openlocfilehash: 622c0cb8d58dddcdb13c7dc9412ada3f2e2d93e4
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632993"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>Az Azure Resource Manager erőforráscsoportok kezelése az Azure Portal használatával

Ismerje meg, hogyan használhatja az [Azure Portalt](https://portal.azure.com) az [Azure Resource Managerrel](overview.md) az Azure-erőforráscsoportok kezeléséhez. Az Azure-erőforrások kezeléséhez lásd: [Azure-erőforrások kezelése az Azure Portal használatával.](manage-resources-portal.md)

További cikkek az erőforráscsoportok kezeléséről:

- [Azure-erőforráscsoportok kezelése az Azure CLI használatával](manage-resources-cli.md)
- [Azure-erőforráscsoportok kezelése az Azure PowerShell használatával](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>Mi az erőforráscsoport?

Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. Az erőforráscsoport tartalmazhatja a megoldás összes erőforrását, vagy csak azokat az erőforrásokat, amelyeket Ön egy csoportként szeretne kezelni. A szervezet számára legideálisabb elosztás alapján eldöntheti, hogyan szeretné elosztani az erőforrásokat az erőforráscsoportok között. Általában adja hozzá az azonos életciklusú erőforrásokat ugyanahhoz az erőforráscsoporthoz, így könnyen telepítheti, frissítheti és törölheti őket csoportként.

Az erőforráscsoport erőforrásokra vonatkozó metaadatokat tárol. Ezért ha az erőforráscsoport számára megad egy helyet, akkor a metaadatok tárolási helyét adja meg. Megfelelőségi okokból szükség lehet arra, hogy az adatokat egy adott régióban tárolja.


## <a name="create-resource-groups"></a>Erőforráscsoportok létrehozása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. **Erőforráscsoportok** kiválasztása

    ![erőforráscsoport hozzáadása](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. Válassza a **Hozzáadás** lehetőséget.
4. Írja be a következő értékeket:

   - **Előfizetés**: Válassza ki az Azure-előfizetést. 
   - **Erőforráscsoport**: Adjon meg egy új erőforráscsoport nevet. 
   - **Régió**: Válasszon ki egy Azure-helyet, például **az USA középső régióját.**

     ![erőforráscsoport létrehozása](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. Vélemény **+ Létrehozás lehetőséget**
6. Kattintson a **Létrehozás** gombra. Az erőforráscsoport létrehozása néhány másodpercet vesz igénybe.
7. Válassza a felső menü **Frissítés** parancsát az erőforráscsoport listájának frissítéséhez, majd az újonnan létrehozott erőforráscsoport kiválasztásával nyissa meg. Vagy válassza felülről az **Értesítés**(a csengő ikon) lehetőséget, majd az Újonnan létrehozott erőforráscsoport megnyitásához válassza az Ugrás az **erőforráscsoporthoz** lehetőséget.

    ![ugrás az erőforráscsoportra](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>Erőforráscsoportok listázása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Az erőforráscsoportok listázásához válassza az **Erőforráscsoportok** lehetőséget.

    ![böngésszen erőforráscsoportok között](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. Az erőforráscsoportokhoz megjelenített adatok testreszabásához válassza az **Oszlopok szerkesztése**lehetőséget. A következő képernyőkép a kijelzőhöz hozzáadható oszlopokat mutatja be:

## <a name="open-resource-groups"></a>Erőforráscsoportok megnyitása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Válassza az **Erőforráscsoportok** lehetőséget.
3. Jelölje ki a megnyitni kívánt erőforráscsoportot.

## <a name="delete-resource-groups"></a>Erőforráscsoportok törlése

1. Nyissa meg a törölni kívánt erőforráscsoportot.  Lásd: [Erőforráscsoportok megnyitása](#open-resource-groups).
2. Válassza az **Erőforráscsoport törlése** elemet.

    ![az azure erőforráscsoport törlése](./media/manage-resource-groups-portal/delete-group.png)

Ha többet szeretne tudni arról, hogy az Azure Resource Manager hogyan rendeli meg az erőforrások törlését, olvassa el az [Azure Resource Manager erőforráscsoport-törlése című témakört.](delete-resource-group.md)

## <a name="deploy-resources-to-a-resource-group"></a>Erőforrások telepítése erőforráscsoportba

Miután létrehozott egy Resource Manager-sablont, az Azure Portal segítségével üzembe helyezheti az Azure-erőforrásokat. Sablon létrehozásáról a [Rövid útmutató: Azure Resource Manager-sablonok létrehozása és üzembe helyezése az Azure Portal használatával](../templates/quickstart-create-templates-use-the-portal.md)című témakörben olvashat. A portál használatával sablon üzembe helyezéséről az [Erőforrások üzembe helyezése erőforrás-kezelői sablonokkal és az Azure Portal használatával című témakörben olvashat.](../templates/deploy-portal.md)

## <a name="move-to-another-resource-group-or-subscription"></a>Áthelyezés másik erőforráscsoportra vagy -előfizetésre

A csoport erőforrásait áthelyezheti egy másik erőforráscsoportba. További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](move-resource-group-and-subscription.md).

## <a name="lock-resource-groups"></a>Erőforráscsoportok zárolása

A zárolás megakadályozza, hogy a szervezet más felhasználói véletlenül kiirtsák vagy módosítsák a kritikus erőforrásokat, például az Azure-előfizetést, az erőforráscsoportot vagy az erőforrást. 

1. Nyissa meg a törölni kívánt erőforráscsoportot.  Lásd: [Erőforráscsoportok megnyitása](#open-resource-groups).
2. A bal oldali ablaktáblában válassza a **Zárolások**lehetőséget.
3. Ha zárolást szeretne hozzáadni az erőforráscsoporthoz, válassza a **Hozzáadás**lehetőséget.
4. Írja be **a Zárolás nevét**, **zárolási típusát**és a Jegyzetek **értéket.** A zárolási típusok közé tartozik **az írásvédett**és a **Törlés**.

    ![zárolja az azure erőforráscsoportot](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

További információt az [Erőforrások zárolása a váratlan módosítások megelőzése érdekében](lock-resources.md)című témakörben talál.

## <a name="tag-resource-groups"></a>Erőforráscsoportok címkézése

Címkéket alkalmazhat az erőforráscsoportokra és erőforrásokra az eszközök logikai rendszerezéséhez. További információ: [Címkék használata az Azure-erőforrások rendszerezéséhez című témakörben.](tag-resources.md#portal)

## <a name="export-resource-groups-to-templates"></a>Erőforráscsoportok exportálása sablonokba

A sablonok exportálásáról az [Egy- és többerőforrásos exportálás sablonba – Portál](../templates/export-template-portal.md)című témakörben olvashat.

## <a name="manage-access-to-resource-groups"></a>Erőforráscsoportokhoz való hozzáférés kezelése

[A szerepköralapú hozzáférés-vezérlés (RBAC) az Azure-beli](../../role-based-access-control/overview.md) erőforrásokhoz való hozzáférés kezelésének módja. További információkért lásd [a hozzáférés az RBAC és az Azure Portal használatával történő kezelését](../../role-based-access-control/role-assignments-portal.md) ismertető cikket.

## <a name="next-steps"></a>További lépések

- Az Azure Resource Manager megismeréséhez olvassa el az [Azure Resource Manager áttekintése című témakört.](overview.md)
- Az Erőforrás-kezelő sablon szintaxisának megismeréséről [Az Azure Resource Manager-sablonok szerkezetének és szintaxisának megismerése című témakörben olvashat.](../templates/template-syntax.md)
- A sablonok fejlesztéséről részletesen olvashat [az oktatóanyagokról.](/azure/azure-resource-manager/)
- Az Azure Resource Manager sablonsémák megtekintéséhez olvassa el a [sablon hivatkozási .](/azure/templates/)
