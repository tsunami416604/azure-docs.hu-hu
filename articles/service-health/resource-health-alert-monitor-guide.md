---
title: Resource Health riasztások létrehozása az Azure Portal használatával
description: Riasztás létrehozása Azure Portal használatával, amely értesíti Önt, ha az Azure-erőforrások elérhetetlenné válnak.
ms.topic: conceptual
ms.date: 6/23/2020
ms.openlocfilehash: 2ba7597283ed254b8c03e8ea58c3c7b1257250ee
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85218037"
---
# <a name="configure-resource-health-alerts-using-azure-portal"></a>Erőforrás-állapotra vonatkozó riasztások konfigurálása Azure Portal használatával

Ebből a cikkből megtudhatja, hogyan állíthatja be a műveletnapló riasztásait az erőforrás-állapotra vonatkozó értesítésekhez a Azure Portal használatával.

Azure Resource Health folyamatosan tájékoztat az Azure-erőforrások aktuális és korábbi állapotáról. Azure Resource Health riasztások közel valós időben értesítik Önt, ha az erőforrások állapota megváltozik. Resource Health riasztások létrehozása programozott módon lehetővé teszi a felhasználók számára a riasztások tömeges létrehozását és testreszabását.

> [!NOTE]
> Resource Health riasztás jelenleg előzetes verzióban érhető el.

Az erőforrás-állapotra vonatkozó értesítések tárolása az [Azure-tevékenység naplójában](https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview) történik, mivel a tevékenység naplójában tárolt adatok valószínűleg nagy mennyiségű információval rendelkeznek, külön felhasználói felület áll rendelkezésre, amely megkönnyíti a riasztások megtekintését és beállítását az erőforrás-állapottal kapcsolatos értesítéseken.
Riasztást kaphat, ha az Azure-erőforrás Resource Health-értesítéseket küld az Azure-előfizetésre. A riasztást a következő alapján állíthatja be:

* Az érintett előfizetés.
* Az erőforrás (ok) típusa érintett.
* Az érintett erőforráscsoport (ok).
* Az érintett erőforrás (ok).
* Az érintett erőforrás (ok) eseményének állapota (i).
* Az érintett erőforrás (ok) állapota.
* Az érintett erőforrás (ok) (ok) típusa (i).

Azt is beállíthatja, hogy a rendszer kik számára küldje a riasztást:

* Válasszon ki egy meglévő műveleti csoportot.
* Hozzon létre egy új műveleti csoportot (amely a jövőbeli riasztásokhoz használható).

A műveletcsoportokkal kapcsolatban további információt a [műveletcsoportok létrehozásáról és kezeléséről](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) szóló cikkben talál.

Az erőforrás-állapot értesítési értesítéseinek Azure Resource Manager sablonok használatával történő konfigurálásával kapcsolatos információkért lásd: [Resource Manager-sablonok](https://docs.microsoft.com/azure/service-health/resource-health-alert-arm-template-guide).
Riasztás Resource Health Azure Portal használatával

## <a name="resource-health-alert-using-azure-portal"></a>Riasztás Resource Health az Azure Portal használatával

1. Az Azure [Portalon](https://portal.azure.com/)válassza a **Service Health**lehetőséget.

    ![Service Health kijelölés](./media/resource-health-alert-monitor-guide/service-health-selection.png)
2. A **Resource Health** szakaszban válassza a **Resource Health**lehetőséget.
3. Válassza az **erőforrás hozzáadása állapot riasztás** lehetőséget, és töltse ki a mezőket.
4. A riasztási cél területen válassza ki azt az **előfizetést**, **erőforrástípust**, **erőforráscsoportot** és **erőforrást** , amelyre vonatkozóan riasztást szeretne kapni.

    ![Cél kijelölésének kiválasztása](./media/resource-health-alert-monitor-guide/alert-target.png)

5. A riasztási feltétel területen válassza a következőket:
    1. Az **esemény állapota** , amelyről riasztást szeretne kapni. Az esemény súlyossági szintje: aktív, megoldott, folyamatban, frissítve
    2. A riasztáshoz használni kívánt **erőforrás állapota** . Az esemény erőforrás-állapota: elérhető, nem érhető el, ismeretlen, csökkentett teljesítményű
    3. A riasztáshoz használni kívánt **OK típusa** . Az esemény oka: a platform kezdeményezett, a felhasználó által kezdeményezett ![ riasztási feltétel kiválasztása állapot kiválasztása](./media/resource-health-alert-monitor-guide/alert-condition.png)
6. A riasztás részleteinek megadása területen adja meg a következő adatokat:
    1. **Riasztási szabály neve**: az új riasztási szabály neve.
    2. **Leírás**: az új riasztási szabály leírása.
    3. **Riasztás mentése az erőforráscsoporthoz**: válassza ki azt az erőforráscsoportot, amelybe menteni szeretné ezt az új szabályt.
7. A **Művelettípus**alatt, a legördülő menüből válassza ki azt a műveleti csoportot, amelyet hozzá szeretne rendelni ehhez az új riasztási szabályhoz. Vagy [hozzon létre egy új műveleti csoportot](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) , és rendelje hozzá az új szabályhoz. Új csoport létrehozásához válassza az + **új csoport**lehetőséget.
8. Ha a létrehozás után engedélyezni szeretné a szabályokat, válassza az **Igen** lehetőséget a **szabály engedélyezése a létrehozáskor** beállításnál.
9. Válassza a **Riasztási szabály létrehozása** lehetőséget.

A rendszer létrehozza a műveletnapló új riasztási szabályát, és egy megerősítő üzenet jelenik meg az ablak jobb felső sarkában.
Engedélyezheti, letilthatja, szerkesztheti vagy törölheti a szabályokat. További információ a [műveletnapló szabályainak kezeléséről](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log#view-and-manage-in-the-azure-portal).

## <a name="next-steps"></a>További lépések

További információ a Resource Healthról:

* [Azure Resource Health áttekintése](Resource-health-overview.md)
* [Az Azure Resource Health segítségével elérhető erőforrástípusok és állapotellenőrzések](resource-health-checks-resource-types.md)

Service Health riasztások létrehozása:

* [Riasztások konfigurálása Service Healthhoz](../azure-monitor/platform/alerts-activity-log-service-notifications.md) 
* [Azure Activity log esemény sémája](../azure-monitor/platform/activity-log-schema.md)
* [Erőforrás-állapotra vonatkozó riasztások konfigurálása Resource Manager-sablonok használatával](https://docs.microsoft.com/azure/service-health/resource-health-alert-arm-template-guide)