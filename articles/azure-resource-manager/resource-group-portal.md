---
title: Azure-erőforrások kezelése az Azure portal használatával |} A Microsoft Docs
description: Az Azure portal és az Azure Resource Manager használatával kezelheti az erőforrásokat. Bemutatja, hogyan működik az erőforrások figyelése az irányítópultok.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38600361"
---
# <a name="manage-azure-resources-through-portal"></a>Azure-portálon keresztül erőforrások kezelése

Ez a cikk bemutatja, hogyan használhatja a [az Azure portal](https://portal.azure.com) a [Azure Resource Manager](resource-group-overview.md) az Azure-erőforrások kezeléséhez. Tekintse meg, hogyan helyezhető üzembe az erőforrásoknak a portálon keresztül, [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure portal](resource-group-template-deploy-portal.md).

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="manage-resource-groups"></a>Erőforráscsoportok kezelése

Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. Az erőforráscsoport tartalmazhatja a megoldás összes erőforrását, vagy csak azokat az erőforrásokat, amelyeket Ön egy csoportként szeretne kezelni. A szervezet számára legideálisabb elosztás alapján eldöntheti, hogyan szeretné elosztani az erőforrásokat az erőforráscsoportok között. Általánosságban elmondható adjon hozzá erőforrásokat, azonos életciklussal ugyanabban az erőforráscsoportban, így könnyen telepítése, frissítése és csoportként törölheti őket. 

Az erőforráscsoport erőforrásokra vonatkozó metaadatokat tárol. Ezért ha az erőforráscsoport számára megad egy helyet, akkor a metaadatok tárolási helyét adja meg. Megfelelőségi okokból szükség lehet arra, hogy az adatokat egy adott régióban tárolja.

1. Az előfizetés összes erőforráscsoportjában megtekintéséhez válasszon **erőforráscsoportok**.
   
    ![Keresse meg az erőforráscsoportok](./media/resource-group-portal/browse-groups.png)
2. Üres erőforráscsoport létrehozásához válassza **Hozzáadás**.
   
    ![erőforráscsoport hozzáadása](./media/resource-group-portal/add-resource-group.png)
3. Adjon meg egy nevet és az új erőforráscsoport helyét. Kattintson a **Létrehozás** gombra.
   
    ![Erőforráscsoport létrehozása](./media/resource-group-portal/create-empty-group.png)
4. Előfordulhat, hogy ki kell választania **frissítése** a legutóbb létrehozott erőforráscsoport megtekintéséhez.
   
    ![Erőforráscsoport frissítése](./media/resource-group-portal/refresh-resource-groups.png)
5. Az erőforráscsoportok megjelenő információk testreszabásához válassza **oszlopok**.
   
    ![oszlopok testreszabása](./media/resource-group-portal/select-columns.png)
6. Adja hozzá, és válassza ki a kívánt oszlopok **frissítés**.
   
    ![oszlopok hozzáadása](./media/resource-group-portal/add-columns.png)
7. További információ az új erőforráscsoportban való üzembe helyezni erőforrásokat,: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure portal](resource-group-template-deploy-portal.md).
8. Egy erőforráscsoport gyors eléréséhez az erőforráscsoport rögzítheti az irányítópulton.
   
    ![PIN-kód erőforráscsoport](./media/resource-group-portal/pin-group.png)
9. Az irányítópult megjeleníti az erőforráscsoport és az erőforrások. Kiválaszthatja az erőforráscsoportok vagy a hozzá tartozó erőforrások nyissa meg a cikkhez.
   
    ![PIN-kód erőforráscsoport](./media/resource-group-portal/show-resource-group-dashboard.png)

## <a name="tag-resources"></a>Erőforrások címkézése
A címkékkel erőforráscsoportok és erőforrásokhoz, hogy logikusan rendszerezhesse az eszközöket. A címkék használatáról további információért lásd: [az Azure-erőforrások rendszerezése címkék használatával](resource-group-using-tags.md).

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="monitor-resources"></a>Erőforrások megfigyelése
Amikor kiválaszt egy erőforrást, a portál megjelenít alapértelmezett diagram és táblák figyeléshez az erőforrás típusát.

1. Válasszon ki egy erőforrást, és figyelje meg a **figyelés** szakaszban. Ez magában foglalja, amely az erőforrás típusát a gráfok. Az alábbi képen látható, azt az alapértelmezett tárfiók adatait.
   
    ![figyelés megjelenítése](./media/resource-group-portal/show-monitoring.png)
2. Válassza a három pontra (...) a szakasz fenti szakasz is rögzíthet az irányítópulton. Testre szabhatja a méretet a szakasz is, vagy távolítsa el teljesen. A következő kép bemutatja, hogyan rögzítése, testre szabhatja, vagy távolítsa el a CPU és memória szakaszban.
   
    ![PIN-kód szakaszban](./media/resource-group-portal/pin-cpu-section.png)
3. A szakasz az irányítópulton rögzíthet, után látni fogja az összefoglaló az irányítópulton. És azonnal kiválasztva megnyílik az adatokkal kapcsolatos további részletekért.
   
    ![Irányítópult megtekintése](./media/resource-group-portal/view-startboard.png)
4. Teljes mértékben testre szabhatja az adatok a portálon keresztül figyelheti, keresse meg az alapértelmezett irányítópultot, és válassza ki **új irányítópult**.
   
    ![irányítópult](./media/resource-group-portal/dashboard.png)
5. Adja meg az új irányítópult nevét, és húzzon csempét az irányítópulton. A csempék különböző beállításai alapján vannak szűrve.
   
    ![irányítópult](./media/resource-group-portal/create-dashboard.png)
   
     Az irányítópultok használata kapcsolatos további információkért lásd: [létrehozása és az Azure Portalon irányítópultok megosztásával](../azure-portal/azure-portal-dashboards.md).

## <a name="manage-resources"></a>Erőforrások kezelése
A portálon egy erőforrás megtekintésekor láthatja az adott erőforráshoz kezelésére vonatkozó beállítások.

![erőforrások kezelése](./media/resource-group-portal/manage-resources.png)

Ezek a beállítások a műveletek, például a elindítása és a egy virtuális gép leállítása vagy újrakonfigurálása a virtuális gép tulajdonságai között végezheti.

## <a name="move-resources"></a>Erőforrások áthelyezése
Ha az erőforrások áthelyezése másik erőforráscsoportba vagy másik előfizetésre van szüksége, tekintse meg [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](resource-group-move-resources.md).

## <a name="lock-resources"></a>Erőforrások zárolása
Zárolhatja egy előfizetés, erőforráscsoport vagy erőforrás véletlen törlését vagy módosítását kiemelt fontosságú erőforrásait a munkahely más felhasználóinak elkerülése érdekében. További információ: [Erőforrások zárolása az Azure Resource Manager eszközzel](resource-group-lock-resources.md).

[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="view-your-subscription-and-costs"></a>Az előfizetés és a költségek megtekintése
Információk az előfizetés és az összegzett költségeinek az összes erőforrás megtekintése. Válassza ki **előfizetések** , és meg szeretné tekinteni az előfizetést. Előfordulhat, hogy csak válassza ki az előfizetéssel rendelkezik.

![előfizetést](./media/resource-group-portal/select-subscription.png)

Láthatja, hogy az írási sebessége.

![írási sebessége](./media/resource-group-portal/burn-rate.png)

És költségeket erőforrástípus szerint.

![Erőforrás-költség](./media/resource-group-portal/cost-by-resource.png)

## <a name="export-template"></a>Sablon exportálása
Miután beállította az erőforráscsoportban, érdemes a Resource Manager-sablon az erőforráscsoport megtekintéséhez. A sablon exportálása két előnyöket kínál:

1. A megoldás a későbbiekben a sablon tartalmaz, a teljes infrastruktúra könnyedén automatizálható.
2. Válhat ismeri a sablon szintaxisát alapján, a JavaScript Object Notation (JSON), amely a megoldás jelöli.

Lépésenkénti útmutatásért lásd: [Azure Resource Manager sablon exportálása létező erőforrásokból](resource-manager-export-template.md).

## <a name="delete-resource-group-or-resources"></a>Erőforráscsoport és erőforrások törlése
Egy erőforráscsoport törlése törli a benne található összes erőforrást. Törölheti is az egyes erőforrások erőforráscsoporton belül. Legyen körültekintő, amikor egy erőforráscsoport törlése. Erőforráscsoport tartalmazhatja az erőforrás más erőforráscsoportok függő erőforrások.

![csoport törlése](./media/resource-group-portal/delete-group.png)

## <a name="next-steps"></a>További lépések
* Tevékenységnaplók megtekintése, lásd: [auditálási műveletek a Resource Manager](resource-group-audit.md).
* Központi telepítés részleteinek megtekintéséhez: [üzembehelyezési műveletek megtekintése](resource-manager-deployment-operations.md).
* Az erőforrásoknak a portálon keresztül történő központi telepítéséhez lásd: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure portal](resource-group-template-deploy-portal.md).
* Erőforrásokhoz való hozzáférés kezelése, tekintse meg [az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése a szerepkör-hozzárendelésekkel](../role-based-access-control/role-assignments-portal.md).
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.

