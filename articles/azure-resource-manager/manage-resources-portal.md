---
title: Azure-erőforrások kezelése az Azure portal használatával |} A Microsoft Docs
description: Az Azure portal és az Azure Resource Manager használatával kezelheti az erőforrásokat.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 20bf38b87ce29f8506a5611ecd25cf38f6d4ed61
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56825240"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>Azure-erőforrások kezelése az Azure portal használatával

Ismerje meg, hogyan használható a [az Azure portal](https://portal.azure.com) a [Azure Resource Manager](resource-group-overview.md) az Azure-erőforrások kezeléséhez. Erőforráscsoportok kezeléséhhez lásd: [kezelése Azure-erőforráscsoportok az Azure portal használatával](./manage-resource-groups-portal.md).

Egyéb erőforrások kezelésével kapcsolatos cikkek:

- [Azure-erőforrások kezelése az Azure CLI-vel](./manage-resources-cli.md)
- [Azure-erőforrások kezelése az Azure PowerShell használatával](./manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>Erőforrások üzembe helyezése egy erőforráscsoportot

Resource Manager-sablon létrehozása után az Azure-erőforrások üzembe helyezése az Azure portal használatával. A sablonok létrehozása, lásd: [a rövid útmutató: Létrehozása és üzembe helyezése Azure Resource Manager-sablonok az Azure portal használatával](./resource-manager-quickstart-create-templates-use-the-portal.md). A portál használatával sablonok telepítésével, lásd: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure portal](resource-group-template-deploy-portal.md).

## <a name="open-resources"></a>Nyissa meg erőforrásokat

Azure-erőforrások és erőforráscsoportok Azure-szolgáltatások által vannak rendszerezve. Az alábbi eljárás bemutatja, hogyan nyissa meg a storage-fiók neve **mystorage0207**. A virtuális gép nevű erőforráscsoportban található **mystorage0207rg**.

A szolgáltatás típusának egy erőforrás megnyitása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali panelen válassza ki az Azure-szolgáltatás. Ebben az esetben **tárfiókok**.  Ha nem látja a szolgáltatásnak, válassza ki a **minden szolgáltatás**, majd válassza ki a szolgáltatást.

    ![Nyissa meg azure-erőforráscsoportot a portálon](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. Válassza ki a megnyitni kívánt erőforrást.

    ![Nyissa meg azure-erőforráscsoportot a portálon](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    Storage-fiók hasonlóan néz ki:

    ![Nyissa meg azure-erőforráscsoportot a portálon](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

Erőforrás szerint erőforráscsoport megnyitása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali panelen válassza ki a **erőforráscsoportok** listázásához az erőforrás a csoporton belül.
3. Válassza ki a megnyitni kívánt erőforrást. 

## <a name="manage-resources"></a>Erőforrások kezelése

A portálon egy erőforrás megtekintésekor láthatja az adott erőforráshoz kezelésére vonatkozó beállítások.

![Azure-erőforrások kezelése](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

A képernyőfelvételen a felügyeleti lehetőségeket az Azure virtuális gépként. Például indítása, az újraindítás és a egy virtuális gép leállítása műveleteket hajthat végre.

## <a name="delete-resources"></a>Erőforrások törlése

1. Nyissa meg az erőforrást a portálon. A lépésekért lásd: [megnyithat erőforrásokat](#open-resources).
2. Válassza a **Törlés** elemet. Az alábbi képernyőképen látható a virtuális gép felügyeleti lehetőségeket.

    ![az azure-erőforrás törlése](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. Adja meg a törlés jóváhagyásához, és válassza ki az erőforrást a **törlése**.

Milyen az Azure Resource Manager orders erőforrások törlését kapcsolatos további információkért lásd: [törölni az Azure Resource Manager-erőforrást csoport](./resource-group-delete.md).

## <a name="move-resources"></a>Erőforrások áthelyezése

1. Nyissa meg az erőforrást a portálon. A lépésekért lásd: [megnyithat erőforrásokat](#open-resources).
2. Válassza ki **áthelyezése**. Az alábbi képernyőfelvételen egy storage-fiók a felügyeleti beállításokat.

    ![azure-erőforrások áthelyezése](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. Válassza ki **áthelyezése másik erőforráscsoportba** vagy **egy másik előfizetéshez Moeve** igényeitől függően.

További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](resource-group-move-resources.md).

## <a name="lock-resources"></a>Erőforrások zárolása

Zárolás megakadályozza a véletlen törlését vagy módosítását a kritikus fontosságú erőforrások, például az Azure-előfizetésre, erőforráscsoportra vagy erőforrásra a szervezet más felhasználói. 

1. Nyissa meg az erőforrást a portálon. A lépésekért lásd: [megnyithat erőforrásokat](#open-resources).
2. Válassza ki **zárolja**. Az alábbi képernyőfelvételen egy storage-fiók a felügyeleti beállításokat.

    ![az azure alkalmazászárolási erőforrás](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. Válassza ki **Hozzáadás**, és adja meg a zárolási tulajdonságait.

További információ: [Erőforrások zárolása az Azure Resource Manager eszközzel](resource-group-lock-resources.md).

## <a name="tag-resources"></a>Erőforrások címkézése

Az erőforráscsoport és erőforrások rendszerezéséhez logikailag címkézési segít. 

1. Nyissa meg az erőforrást a portálon. A lépésekért lásd: [megnyithat erőforrásokat](#open-resources).
2. Válassza ki **címkék**. Az alábbi képernyőfelvételen egy storage-fiók a felügyeleti beállításokat.

    ![a címke az azure-erőforrás](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. Adja meg a címke tulajdonságait, és válassza ki **mentése**.

További információ: [az Azure-erőforrások rendszerezése címkék használatával](./resource-group-using-tags.md#portal).

## <a name="monitor-resources"></a>Erőforrások megfigyelése

Amikor megnyit egy erőforrást, a portál megjelenít alapértelmezett diagram és táblák figyeléshez az erőforrás típusát. Az alábbi képernyőképen egy virtuális gépet a diagramokon látható:

![az azure monitor erőforrás](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

Kiválaszthatja a rajzszög ikonra a diagramok rögzítése az irányítópulton a diagram jobb felső sarkában található. Az irányítópultok használata kapcsolatos további információkért lásd: [létrehozása és az Azure Portalon irányítópultok megosztásával](../azure-portal/azure-portal-dashboards.md).

## <a name="manage-access-to-resources"></a>Az erőforrásokhoz való hozzáférés kezelése

A [szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md) az erőforrásokhoz való hozzáférés kezelésének a módja az Azure-ban. További információkért lásd: [rbac-RÓL és az Azure portal-hozzáférés kezelése](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>További lépések

- Azure Resource Manager kapcsolatban lásd: [Azure Resource Manager áttekintése](./resource-group-overview.md).
- A Resource Manager-sablon szintaxisáról kapcsolatban lásd: [struktúra és az Azure Resource Manager-sablonok szintaxisát](./resource-group-authoring-templates.md).
- Ismerje meg, hogyan fejleszthet sablonokat, tekintse meg a [lépésről lépésre haladó oktatóanyagok](/azure/azure-resource-manager/).
- Az Azure Resource Manager-sablon sémák megtekintése: [sablonreferenciája](/azure/templates/).