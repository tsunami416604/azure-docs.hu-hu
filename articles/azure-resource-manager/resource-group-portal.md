---
title: "Azure-erőforrások kezeléséhez Azure portál használatával |} Microsoft Docs"
description: "Azure-portál és az Azure Resource Manager segítségével kezelheti az erőforrásokat. Bemutatja, hogyan irányítópultok erőforrások figyelésére használható."
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 0725bbf2-5913-4c07-af6e-24e11d957fbc
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: tomfitz
ms.openlocfilehash: 7a94fd5065de93384460e851627a9813d439956b
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="manage-azure-resources-through-portal"></a>Azure-portálon keresztül erőforrások kezelése
> [!div class="op_single_selector"]
> * [Azure PowerShell](powershell-azure-resource-manager.md)
> * [Azure CLI](xplat-cli-azure-resource-manager.md)
> * [Portál](resource-group-portal.md) 
> * [REST API](resource-manager-rest-api.md)
> 
> 

Ez a témakör bemutatja, hogyan használja a [Azure-portálon](https://portal.azure.com) rendelkező [Azure Resource Manager](resource-group-overview.md) az Azure erőforrások kezeléséhez. A portálon keresztül erőforrások telepítésével kapcsolatos további tudnivalókért lásd: [erőforrások a Resource Manager-sablonok és az Azure-portál telepítése](resource-group-template-deploy-portal.md).

Jelenleg nem minden szolgáltatás támogatja, a portál vagy az erőforrás-kezelő. Ezek a szolgáltatások kell használnia a [klasszikus portál](https://manage.windowsazure.com). Minden szolgáltatás állapotát, lásd: [az Azure portál elérhetőségi diagram](https://azure.microsoft.com/features/azure-portal/availability/).

## <a name="manage-resource-groups"></a>Erőforrás-csoportok kezelése

Egy erőforráscsoport egy olyan tároló, amely egy Azure megoldás kapcsolódó erőforrásokat tárol. Az erőforráscsoport tartalmazhatja a megoldás összes erőforrását, vagy csak azokat az erőforrásokat, amelyeket Ön egy csoportként szeretne kezelni. A szervezet számára legideálisabb elosztás alapján eldöntheti, hogyan szeretné elosztani az erőforrásokat az erőforráscsoportok között. Általában adja hozzá az erőforrásokat, amelyek ugyanabban az erőforráscsoportban az azonos életciklussal megoszthatja, így könnyen központi telepítése, frissítése és csoportként törölje őket. 

Az erőforráscsoport erőforrásokra vonatkozó metaadatokat tárol. Ezért ha az erőforráscsoport számára megad egy helyet, akkor a metaadatok tárolási helyét adja meg. Megfelelőségi okokból szükség lehet arra, hogy az adatokat egy adott régióban tárolja.

1. Az előfizetés összes erőforráscsoport megtekintéséhez válasszon **erőforráscsoportok**.
   
    ![Keresse meg az erőforráscsoport-sablonok](./media/resource-group-portal/browse-groups.png)
2. Hozzon létre egy üres erőforráscsoportot, válassza ki **Hozzáadás**.
   
    ![csoport hozzáadása](./media/resource-group-portal/add-resource-group.png)
3. Adjon meg nevet és az új erőforráscsoporthoz tartozó helyet. Kattintson a **Létrehozás** gombra.
   
    ![Erőforráscsoport létrehozása](./media/resource-group-portal/create-empty-group.png)
4. Válassza ki szeretne **frissítése** a legutóbb létrehozott erőforráscsoport megtekintéséhez.
   
    ![erőforrás-csoport frissítése](./media/resource-group-portal/refresh-resource-groups.png)
5. Az erőforráscsoportok megjelenített információk testreszabásához jelölje be **oszlopok**.
   
    ![oszlop testreszabása](./media/resource-group-portal/select-columns.png)
6. A hozzáadása, és válasszon egy oszlopot válasszon ki **frissítés**.
   
    ![oszlopok hozzáadása](./media/resource-group-portal/add-columns.png)
7. Az új erőforráscsoportba erőforrások telepítésével kapcsolatos további tudnivalókért lásd: [erőforrások a Resource Manager-sablonok és az Azure-portál telepítése](resource-group-template-deploy-portal.md).
8. A gyors elérés érdekében az erőforráscsoporthoz a panel rögzítheti az irányítópulton való rögzítéséhez.
   
    ![PIN-kód erőforráscsoport](./media/resource-group-portal/pin-group.png)
9. Az irányítópult az erőforráscsoportot és az erőforrások jeleníti meg. Kiválaszthatja az erőforráscsoportok vagy az erőforrásokat a navigáljon.
   
    ![PIN-kód erőforráscsoport](./media/resource-group-portal/show-resource-group-dashboard.png)

## <a name="tag-resources"></a>Címke erőforrások
A címkékkel erőforráscsoportok és erőforrásokhoz, hogy logikusan rendszerezhesse az Ön eszközeit. A címkék használatáról információkért lásd: [az Azure-erőforrások rendszerezése címkék használatával](resource-group-using-tags.md).

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="monitor-resources"></a>Erőforrások megfigyelése
Amikor kiválaszt egy erőforrást, az erőforrás panel alapértelmezett diagramjait és az adott erőforrástípus figyelési táblák mutatja be.

1. Válasszon ki egy erőforrást és értesítés a **figyelés** szakasz. Ez magában foglalja a diagramok, amelyek kapcsolódnak az erőforrástípus. A következő kép bemutatja az alapértelmezett figyelési a tárfiók adatait.
   
    ![figyelési megjelenítése](./media/resource-group-portal/show-monitoring.png)
2. Egy részében találhatja; ehhez válassza a három ponttal (…) a szakasz fenti rögzítheti az irányítópulton. A szakasz a panel mérete testreszabásával, vagy távolítsa el teljesen. A következő kép bemutatja, hogyan PIN-kód, testre szabhatja, vagy távolítsa el a Processzor és memória szakaszban.
   
    ![PIN-kód szakasz](./media/resource-group-portal/pin-cpu-section.png)
3. Miután a szakasz az irányítópulton, látni fogja az összegzés az irányítópulton. És lehetőséget azonnal viszi az adatokkal kapcsolatos további részletekért.
   
    ![Irányítópult nézet](./media/resource-group-portal/view-startboard.png)
4. A portálon keresztül figyelheti az adatok teljesen testreszabásához nyissa meg az alapértelmezett irányítópultot, és válassza ki **új irányítópult**.
   
    ![irányítópult](./media/resource-group-portal/dashboard.png)
5. Nevezze el az új irányítópult és csempék húzza az irányítópulton. A csempék különböző beállítások szerint vannak szűrve.
   
    ![irányítópult](./media/resource-group-portal/create-dashboard.png)
   
     Irányítópultok kezelésével kapcsolatos információkért lásd: [létrehozása és az Azure portálon irányítópultok megosztása](../azure-portal/azure-portal-dashboards.md).

## <a name="manage-resources"></a>Erőforrások kezelése
Az erőforrás panelen tekintse meg a beállítások kezelését az erőforrás. A portál megjelenít, hogy adott erőforrástípusra vonatkozó beállítások. A parancsok és a bal oldalon az erőforrás panel tetején megjelenik.

![Erőforrások kezelése](./media/resource-group-portal/manage-resources.png)

Ezek a beállítások műveletek, például a indítása és leállítása a virtuális gép vagy újrakonfigurálása a virtuális gép tulajdonságai között végezheti el.

## <a name="move-resources"></a>Erőforrások áthelyezése
Ha az erőforrások áthelyezése egy másik erőforráscsoportban vagy egy másik előfizetés van szüksége, tekintse meg [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](resource-group-move-resources.md).

## <a name="lock-resources"></a>Erőforrások zárolása
Zárolhatja egy előfizetés, erőforráscsoportból vagy erőforrás véletlen törlése vagy a kritikus erőforrásokat módosítása a munkahely más felhasználóinak megelőzése érdekében. További információ: [Erőforrások zárolása az Azure Resource Manager eszközzel](resource-group-lock-resources.md).

[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="view-your-subscription-and-costs"></a>Az előfizetés és a költségek megtekintése
Az előfizetés és az összegzett költségeinek kapcsolatos információk is megtekinthetők az erőforrások. Válassza ki **előfizetések** és meg szeretné tekinteni az előfizetést. Válasszon egy előfizetés csak lehet.

![előfizetést](./media/resource-group-portal/select-subscription.png)

Az előfizetés panel belül megjelenik egy írási sebesség.

![Írás gyakorisága](./media/resource-group-portal/burn-rate.png)

És erőforrástípusok szerint költségeket.

![Erőforrás költség](./media/resource-group-portal/cost-by-resource.png)

## <a name="export-template"></a>Sablon exportálása
Miután beállította az erőforráscsoport, érdemes lehet a Resource Manager-sablon az erőforráscsoport megtekintéséhez. A sablon exportálása két előnyökkel jár:

1. Könnyen automatizálható a későbbi központi telepítés alkalmával a megoldás, mert a sablon tartalmazza a teljes infrastruktúra.
2. Válhat ismeri a sablon szintaxisát, a JavaScript Object Notation (JSON) a megoldás jelölő megkeresésével.

Lépésenkénti útmutatásért lásd: [Azure Resource Manager sablon exportálása létező erőforrásokból](resource-manager-export-template.md).

## <a name="delete-resource-group-or-resources"></a>Erőforráscsoport és erőforrások törlése
Erőforráscsoport törlésekor törlődnek a benne található összes erőforrást. Törölheti az egyes erőforrások erőforráscsoporton belül is. Szeretné körültekintően járjon el egy erőforráscsoport törlésekor, mert lehetséges, hogy nem erőforrások egyéb erőforráscsoportok, amelyek kapcsolódnak. Erőforrás-kezelő nem törli a társított erőforrásokat, de azok nem működnek megfelelően nélkül szükséges erőforrásokkal.

![Csoport törlése](./media/resource-group-portal/delete-group.png)

## <a name="next-steps"></a>Következő lépések
* Tevékenységi naplóit megtekintéséhez lásd: [naplózási műveletek a Resource Manager](resource-group-audit.md).
* A központi telepítés részleteinek megtekintéséhez lásd: [üzembe helyezési műveleteinek megtekintése](resource-manager-deployment-operations.md).
* Az erőforrásoknak a portálon keresztül történő központi telepítéséhez lásd: [erőforrások a Resource Manager-sablonok és az Azure-portál telepítése](resource-group-template-deploy-portal.md).
* Erőforrások elérésének kezelésében, tekintse meg [az Azure-előfizetés erőforrásokhoz való hozzáférés kezelése a szerepkör-hozzárendelések segítségével](../active-directory/role-based-access-control-configure.md).
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.

