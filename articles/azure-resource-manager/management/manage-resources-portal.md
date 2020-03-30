---
title: Erőforrások kezelése – Azure Portal
description: Használja az Azure Portalon és az Azure Resource Manager az erőforrások kezeléséhez. Az erőforrások üzembe helyezése és törlése.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 00d260a74807774d5bf226c3ec00a6b84f93b8d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248345"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>Azure-erőforrások kezelése az Azure Portal használatával

Ismerje meg, hogyan használhatja az [Azure Portalt](https://portal.azure.com) az [Azure Resource Managerrel](overview.md) az Azure-erőforrások kezeléséhez. Az erőforráscsoportok kezeléséről az [Azure-erőforráscsoportok kezelése az Azure Portal használatával](manage-resource-groups-portal.md)című témakörben található.

További cikkek az erőforrások kezeléséről:

- [Azure-erőforrások kezelése az Azure CLI használatával](manage-resources-cli.md)
- [Azure-erőforrások kezelése az Azure PowerShell használatával](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>Erőforrások telepítése erőforráscsoportba

Miután létrehozott egy Resource Manager-sablont, az Azure Portal segítségével üzembe helyezheti az Azure-erőforrásokat. Sablon létrehozásáról a [Rövid útmutató: Azure Resource Manager-sablonok létrehozása és üzembe helyezése az Azure Portal használatával](../templates/quickstart-create-templates-use-the-portal.md)című témakörben olvashat. A portál használatával sablon üzembe helyezéséről az [Erőforrások üzembe helyezése erőforrás-kezelői sablonokkal és az Azure Portal használatával című témakörben olvashat.](../templates/deploy-portal.md)

## <a name="open-resources"></a>Erőforrások megnyitása

Az Azure-erőforrások azure-szolgáltatások és erőforráscsoportok szerint vannak rendezve. Az alábbi eljárások bemutatják, hogyan lehet megnyitni egy **mystorage0207**nevű tárfiókot. A virtuális gép egy **mystorage0207rg**nevű erőforráscsoportban található.

Erőforrás megnyitása a szolgáltatás típusa szerint:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. A bal oldali ablaktáblában válassza ki az Azure-szolgáltatást. Ebben az esetben **a Storage-fiókok.**  Ha nem látja a szolgáltatás szerepel, válassza a **Minden szolgáltatás**lehetőséget, majd válassza ki a szolgáltatás típusát.

    ![nyílt azure erőforrás a portálon](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. Jelölje ki a megnyitni kívánt erőforrást.

    ![nyílt azure erőforrás a portálon](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    A tárfiók így néz ki:

    ![nyílt azure erőforrás a portálon](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

Erőforrás megnyitása erőforráscsoport onként:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. A bal oldali ablaktáblában válassza az **Erőforráscsoportok** lehetőséget az erőforrás csoporton belüli listázásához.
3. Jelölje ki a megnyitni kívánt erőforrást. 

## <a name="manage-resources"></a>Erőforrások kezelése

Amikor megtekint egy erőforrást a portálon, láthatja az adott erőforrás kezelésének lehetőségeit.

![Azure-erőforrások kezelése](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

A képernyőkép egy Azure virtuális gép felügyeleti beállításait jeleníti meg. Olyan műveleteket hajthat végre, mint például a virtuális gépek indítása, újraindítása és leállítása.

## <a name="delete-resources"></a>Erőforrások törlése

1. Nyissa meg az erőforrást a portálon. A lépéseket az [Erőforrások megnyitása](#open-resources)című témakörben található.
2. Válassza a **Törlés** elemet. A következő képernyőképen a virtuális gépek felügyeleti beállításai láthatók.

    ![az azure-erőforrás törlése](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. Írja be az erőforrás nevét a törlés megerősítéséhez, majd kattintson a **Törlés gombra.**

Ha többet szeretne tudni arról, hogy az Azure Resource Manager hogyan rendeli meg az erőforrások törlését, olvassa el az [Azure Resource Manager erőforráscsoport-törlése című témakört.](delete-resource-group.md)

## <a name="move-resources"></a>Erőforrások áthelyezése

1. Nyissa meg az erőforrást a portálon. A lépéseket az [Erőforrások megnyitása](#open-resources)című témakörben található.
2. Válassza az **Áthelyezés**lehetőséget. A következő képernyőképen egy tárfiók felügyeleti beállításai láthatók.

    ![az azure-erőforrás áthelyezése](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. Válassza **az Áthelyezés másik erőforráscsoportba** lehetőséget, vagy az **Igényszerint válassza az Áthelyezés másik előfizetésre** lehetőséget.

További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](move-resource-group-and-subscription.md).

## <a name="lock-resources"></a>Erőforrások zárolása

A zárolás megakadályozza, hogy a szervezet más felhasználói véletlenül kiirtsák vagy módosítsák a kritikus erőforrásokat, például az Azure-előfizetést, az erőforráscsoportot vagy az erőforrást. 

1. Nyissa meg az erőforrást a portálon. A lépéseket az [Erőforrások megnyitása](#open-resources)című témakörben található.
2. Válassza **a Zárolások**lehetőséget. A következő képernyőképen egy tárfiók felügyeleti beállításai láthatók.

    ![zárolja az azure-erőforrást](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. Válassza **a Hozzáadás**lehetőséget, majd adja meg a zárolási tulajdonságokat.

További információ: [Erőforrások zárolása az Azure Resource Manager eszközzel](lock-resources.md).

## <a name="tag-resources"></a>Erőforrások címkézése

A címkézés segít az erőforráscsoport és az erőforrások logikus rendszerezésben. 

1. Nyissa meg az erőforrást a portálon. A lépéseket az [Erőforrások megnyitása](#open-resources)című témakörben található.
2. Válassza a **Címkék** lehetőséget. A következő képernyőképen egy tárfiók felügyeleti beállításai láthatók.

    ![címke azure erőforrás](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. Adja meg a címke tulajdonságait, majd válassza a **Mentés gombot.**

További információ: [Címkék használata az Azure-erőforrások rendszerezéséhez című témakörben.](tag-resources.md#portal)

## <a name="monitor-resources"></a>Erőforrások megfigyelése

Amikor megnyit egy erőforrást, a portál bemutatja az alapértelmezett grafikonokat és táblákat az adott erőforrástípus figyelésére. A következő képernyőkép egy virtuális gép grafikonjait mutatja be:

![figyelés, azure-erőforrás](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

A grafikonok jobb felső sarkában lévő pin ikont kiválaszthatja a diagram irányítópultra rögzítéséhez. Az irányítópultok használatával kapcsolatos tudnivalók: [Irányítópultok létrehozása és megosztása az Azure Portalon.](../../azure-portal/azure-portal-dashboards.md)

## <a name="manage-access-to-resources"></a>Erőforrásokhoz való hozzáférés kezelése

[A szerepköralapú hozzáférés-vezérlés (RBAC) az Azure-beli](../../role-based-access-control/overview.md) erőforrásokhoz való hozzáférés kezelésének módja. További információkért lásd [a hozzáférés az RBAC és az Azure Portal használatával történő kezelését](../../role-based-access-control/role-assignments-portal.md) ismertető cikket.

## <a name="next-steps"></a>További lépések

- Az Azure Resource Manager megismeréséhez olvassa el az [Azure Resource Manager áttekintése című témakört.](overview.md)
- Az Erőforrás-kezelő sablon szintaxisának megismeréséről [Az Azure Resource Manager-sablonok szerkezetének és szintaxisának megismerése című témakörben olvashat.](../templates/template-syntax.md)
- A sablonok fejlesztéséről részletesen olvashat [az oktatóanyagokról.](/azure/azure-resource-manager/)
- Az Azure Resource Manager sablonsémák megtekintéséhez olvassa el a [sablon hivatkozási .](/azure/templates/)
