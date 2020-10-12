---
title: Műveletnapló-riasztások létrehozása a laborokhoz Azure DevTest Labs
description: Ez a cikk a Azure DevTest Labs a laborhoz tartozó műveletnapló-riasztások létrehozásának lépéseit ismerteti.
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: d5886ea26ddbeb07efc23d61d3197860620eebf3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90526357"
---
# <a name="create-activity-log-alerts-for-labs-in-azure-devtest-labs"></a>Műveletnapló-riasztások létrehozása a laborokhoz Azure DevTest Labs
Ez a cikk azt ismerteti, hogyan lehet tevékenység-naplózási riasztásokat létrehozni a laborokhoz Azure DevTest Labsban (például virtuális gép létrehozásakor vagy egy virtuális gép törlésekor).

## <a name="create-alerts"></a>Riasztások létrehozása
Ebben a példában riasztást hoz létre az összes felügyeleti művelethez egy tesztkörnyezetben egy olyan művelettel, amely e-mailt küld az előfizetés-tulajdonosoknak. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A Azure Portal keresési sávjában írja be a **monitor**kifejezést, majd válassza a **figyelő** lehetőséget az eredmények listából. 

    :::image type="content" source="./media/activity-logs/search-monitor.png" alt-text="Figyelő keresése":::        
1. Válassza a bal oldali menüben a **riasztások** elemet, majd az eszköztáron válassza az **új riasztási szabály** lehetőséget. 

    :::image type="content" source="./media/activity-logs/alerts-page.png" alt-text="Figyelő keresése":::    
1. A **riasztási szabály létrehozása** lapon kattintson az **erőforrás kiválasztása**elemre. 

    :::image type="content" source="./media/activity-logs/select-resource-link.png" alt-text="Figyelő keresése":::        
1. Válassza a **DevTest Labs** lehetőséget az **erőforrástípus szerinti szűréshez**, válassza ki a labort a listából, majd válassza a **kész**lehetőséget.

    :::image type="content" source="./media/activity-logs/select-lab-resource.png" alt-text="Figyelő keresése":::
1. Vissza a **riasztási szabály létrehozása** lapon kattintson a **feltétel kiválasztása**elemre. 

    :::image type="content" source="./media/activity-logs/select-condition-link.png" alt-text="Figyelő keresése":::    
1. A **jel logikai beállítása** lapon válasszon ki egy, a DevTest Labs által támogatott jelet. 

    :::image type="content" source="./media/activity-logs/select-signal.png" alt-text="Figyelő keresése":::
1. Szűrés **eseményvezérelt szint** alapján (részletes, tájékoztató, figyelmeztetés, hiba, kritikus, mind), **állapot** (sikertelen, elindított, sikeres), és **ki kezdeményezte** az eseményt. 
1. A feltétel konfigurálásának befejezéséhez válassza a **kész** lehetőséget. 

    :::image type="content" source="./media/activity-logs/configure-signal-logic-done.png" alt-text="Figyelő keresése":::
1. Meghatározta a hatókört (labor) és a riasztás feltételét. Most meg kell adnia egy műveleti csoportot a feltétel teljesülése esetén futtatandó műveletekkel. Vissza a **riasztási szabály létrehozása** lapon válassza a **műveleti csoport kiválasztása**lehetőséget. 

    :::image type="content" source="./media/activity-logs/select-action-group-link.png" alt-text="Figyelő keresése":::
1. Válassza a **műveleti csoport létrehozása** hivatkozást az eszköztáron. 

    :::image type="content" source="./media/activity-logs/create-action-group-link.png" alt-text="Figyelő keresése":::
1. A **műveleti csoport hozzáadása** oldalon hajtsa végre az alábbi lépéseket:
    1. Adja meg a műveleti csoport **nevét** .
    1. Adja meg a műveleti csoport **rövid nevét** . 
    1. Válassza ki azt az **erőforráscsoportot** , amelyben létre szeretné hozni a riasztást. 
    1. Adja meg **a művelet nevét**. 
    1. Válassza ki a **művelet típusát** (ebben a példában az **e-mail-Azure Resource Manager szerepkör**). 

        :::image type="content" source="./media/activity-logs/add-action-group.png" alt-text="Figyelő keresése":::
    1. Az **e-mail Azure Resource Manager szerepkör** lapon válassza ki a szerepkört. Ebben a példában ez a **tulajdonos**. Ezután kattintson **az OK gombra**. 

        :::image type="content" source="./media/activity-logs/select-role.png" alt-text="Figyelő keresése":::            
    1. A **műveleti csoport hozzáadása** lapon kattintson **az OK gombra** . 
1. Most a **riasztási szabály létrehozása** lapon adja meg a riasztási szabály nevét, majd kattintson az **OK gombra**. 

    :::image type="content" source="./media/activity-logs/create-alert-rule-done.png" alt-text="Figyelő keresése":::

## <a name="view-alerts"></a>Riasztások megtekintése 
1. **A riasztások az összes** felügyeleti művelethez (ebben a példában) jelennek meg. Előfordulhat, hogy a riasztások egy kis ideig eltartanak. 

    :::image type="content" source="./media/activity-logs/alerts.png" alt-text="Figyelő keresése":::
1. Ha a szám elemet választja egy oszlopban (például: **összes riasztás**), akkor a felmerült riasztások megjelennek. 

    :::image type="content" source="./media/activity-logs/all-alerts.png" alt-text="Figyelő keresése":::
1. Ha kijelöl egy riasztást, a rendszer részletes információkat jelenít meg. 

    :::image type="content" source="./media/activity-logs/alert-details.png" alt-text="Figyelő keresése":::
1. Ebben a példában egy e-mailt is kap a tartalommal az alábbi példában látható módon: 

    :::image type="content" source="./media/activity-logs/alert-email.png" alt-text="Figyelő keresése":::

## <a name="next-steps"></a>Következő lépések
- További információ a műveleti csoportok különböző Művelettípus használatával történő létrehozásáról: [műveleti csoportok létrehozása és kezelése a Azure Portalban](../azure-monitor/platform/action-groups.md).
- További információ a tevékenységi naplókról:  [Azure-tevékenység naplója](../azure-monitor/platform/activity-log.md).
- A riasztások a tevékenység naplófájljaiban való beállításával kapcsolatos további információkért lásd: [riasztások a tevékenység naplójában](../azure-monitor/platform/activity-log-alerts.md).

