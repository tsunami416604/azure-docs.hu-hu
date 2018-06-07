---
title: Azure-erőforrások kezeléséhez Azure portál használatával |} Microsoft Docs
description: Azure-portál és az Azure Resource Manager segítségével kezelheti az erőforrásokat. Bemutatja, hogyan irányítópultok erőforrások figyelésére használható.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 0725bbf2-5913-4c07-af6e-24e11d957fbc
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2016
ms.author: tomfitz
ms.openlocfilehash: 7398e01a46b5d296f26905e2063acdb98383f567
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34603385"
---
# <a name="manage-azure-resources-through-portal"></a>Azure-portálon keresztül erőforrások kezelése

Ez a cikk bemutatja, hogyan használható a [Azure-portálon](https://portal.azure.com) rendelkező [Azure Resource Manager](resource-group-overview.md) az Azure erőforrások kezeléséhez. A portálon keresztül erőforrások telepítésével kapcsolatos további tudnivalókért lásd: [erőforrások a Resource Manager-sablonok és az Azure-portál telepítése](resource-group-template-deploy-portal.md).

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="manage-resource-groups"></a>Erőforrás-csoportok kezelése

Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. Az erőforráscsoport tartalmazhatja a megoldás összes erőforrását, vagy csak azokat az erőforrásokat, amelyeket Ön egy csoportként szeretne kezelni. A szervezet számára legideálisabb elosztás alapján eldöntheti, hogyan szeretné elosztani az erőforrásokat az erőforráscsoportok között. Általában adja hozzá az erőforrásokat, amelyek ugyanabban az erőforráscsoportban az azonos életciklussal megoszthatja, így könnyen központi telepítése, frissítése és csoportként törölje őket. 

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
8. A gyors elérés érdekében az erőforráscsoporthoz az erőforráscsoport rögzítheti az irányítópulton való rögzítéséhez.
   
    ![PIN-kód erőforráscsoport](./media/resource-group-portal/pin-group.png)
9. Az irányítópult az erőforráscsoportot és az erőforrások jeleníti meg. Kiválaszthatja az erőforráscsoportok vagy az erőforrásokat a navigáljon.
   
    ![PIN-kód erőforráscsoport](./media/resource-group-portal/show-resource-group-dashboard.png)

## <a name="tag-resources"></a>Erőforrások címkézése
A címkékkel erőforráscsoportok és erőforrásokhoz, hogy logikusan rendszerezhesse az Ön eszközeit. A címkék használatáról információkért lásd: [az Azure-erőforrások rendszerezése címkék használatával](resource-group-using-tags.md).

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="monitor-resources"></a>Erőforrások megfigyelése
Amikor kiválaszt egy erőforrást, a portál megjelenít alapértelmezett diagramjait és táblák figyelés az erőforrás típusát.

1. Válasszon ki egy erőforrást és értesítés a **figyelés** szakasz. Ez magában foglalja a diagramok, amelyek kapcsolódnak az erőforrástípus. A következő kép bemutatja az alapértelmezett figyelési a tárfiók adatait.
   
    ![figyelési megjelenítése](./media/resource-group-portal/show-monitoring.png)
2. Szakasz; ehhez válassza a három ponttal (…) a szakasz fenti rögzítheti az irányítópulton. A szakasz méretét is, vagy távolítsa el teljesen. A következő kép bemutatja, hogyan PIN-kód, testre szabhatja, vagy távolítsa el a Processzor és memória szakaszban.
   
    ![PIN-kód szakasz](./media/resource-group-portal/pin-cpu-section.png)
3. Miután a szakasz az irányítópulton, látni fogja az összegzés az irányítópulton. És lehetőséget azonnal viszi az adatokkal kapcsolatos további részletekért.
   
    ![Irányítópult nézet](./media/resource-group-portal/view-startboard.png)
4. A portálon keresztül figyelheti az adatok teljesen testreszabásához nyissa meg az alapértelmezett irányítópultot, és válassza ki **új irányítópult**.
   
    ![irányítópult](./media/resource-group-portal/dashboard.png)
5. Nevezze el az új irányítópult és csempék húzza az irányítópulton. A csempék különböző beállítások szerint vannak szűrve.
   
    ![irányítópult](./media/resource-group-portal/create-dashboard.png)
   
     Irányítópultok kezelésével kapcsolatos információkért lásd: [létrehozása és az Azure portálon irányítópultok megosztása](../azure-portal/azure-portal-dashboards.md).

## <a name="manage-resources"></a>Erőforrások kezelése
Amikor egy erőforrást a portálon megtekintik, tekintse meg a beállítások kezelését az adott erőforráshoz.

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

Az írási sebesség láthatja.

![Írás gyakorisága](./media/resource-group-portal/burn-rate.png)

És erőforrástípusok szerint költségeket.

![Erőforrás költség](./media/resource-group-portal/cost-by-resource.png)

## <a name="export-template"></a>Sablon exportálása
Miután beállította az erőforráscsoport, érdemes lehet a Resource Manager-sablon az erőforráscsoport megtekintéséhez. A sablon exportálása két előnyökkel jár:

1. Könnyen automatizálható a későbbi központi telepítés alkalmával a megoldás, mert a sablon tartalmazza a teljes infrastruktúra.
2. Válhat ismeri a sablon szintaxisát, a JavaScript Object Notation (JSON) a megoldás jelölő megkeresésével.

Lépésenkénti útmutatásért lásd: [Azure Resource Manager sablon exportálása létező erőforrásokból](resource-manager-export-template.md).

## <a name="delete-resource-group-or-resources"></a>Erőforráscsoport és erőforrások törlése
Erőforráscsoport törlésekor törlődnek a benne található összes erőforrást. Törölheti az egyes erőforrások erőforráscsoporton belül is. Ha töröl egy erőforráscsoport, körültekintően járjon el. Ennek az erőforráscsoportnak is tartalmazhatnak, egyéb erőforráscsoportok erőforrások függő erőforrások.

![Csoport törlése](./media/resource-group-portal/delete-group.png)

## <a name="next-steps"></a>További lépések
* Tevékenységi naplóit megtekintéséhez lásd: [naplózási műveletek a Resource Manager](resource-group-audit.md).
* A központi telepítés részleteinek megtekintéséhez lásd: [üzembe helyezési műveleteinek megtekintése](resource-manager-deployment-operations.md).
* Az erőforrásoknak a portálon keresztül történő központi telepítéséhez lásd: [erőforrások a Resource Manager-sablonok és az Azure-portál telepítése](resource-group-template-deploy-portal.md).
* Erőforrások elérésének kezelésében, tekintse meg [az Azure-előfizetés erőforrásokhoz való hozzáférés kezelése a szerepkör-hozzárendelések segítségével](../role-based-access-control/role-assignments-portal.md).
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.

