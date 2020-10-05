---
title: Azure-erőforrások adatainak megtekintése és szűrése
description: Szűrje az adatokat, és használjon különböző nézeteket az Azure-erőforrások jobb megismeréséhez.
author: mgblythe
ms.service: azure-portal
ms.topic: quickstart
ms.author: mblythe
ms.date: 09/11/2020
ms.openlocfilehash: 3b9783e7f452b38292c784d44ddb60672e150961
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91650398"
---
# <a name="view-and-filter-azure-resource-information"></a>Azure-erőforrások adatainak megtekintése és szűrése

A Azure Portal lehetővé teszi az Azure-előfizetések erőforrásaira vonatkozó részletes információk tallózását. Ez a cikk bemutatja, hogyan szűrheti az adatokat, és hogyan használhatja a különböző nézeteket az erőforrások jobb megismeréséhez.

A cikk a következő képernyőképen látható **összes erőforrás** képernyőre koncentrál. Az egyes erőforrástípusok (például a virtuális gépek) képernyői különböző beállításokkal rendelkeznek, például egy virtuális gép elindítása és leállítása.

:::image type="content" source="media/manage-filter-resource-views/all-resources.png" alt-text="Az összes erőforrás Azure Portal nézete":::

## <a name="filter-resources"></a>Erőforrások szűrése

Az erőforrások egy részhalmazára koncentrálva megkezdheti az **összes erőforrás** felfedezését. Az alábbi képernyőfelvételen az erőforráscsoportok szűrése látható, az előfizetésben található hat erőforráscsoport közül kettőt kiválasztva.

:::image type="content" source="media/manage-filter-resource-views/filter-resource-group.png" alt-text="Az összes erőforrás Azure Portal nézete" elemet tartalmazzák.

:::image type="content" source="media/manage-filter-resource-views/filter-simplewinvm.png" alt-text="Az összes erőforrás Azure Portal nézete":::

Ha módosítani szeretné, hogy mely oszlopok szerepeljenek egy nézetben, válassza a **kezelés nézet** , majd az **Oszlopok szerkesztése**lehetőséget.

:::image type="content" source="media/manage-filter-resource-views/edit-columns.png" alt-text="Az összes erőforrás Azure Portal nézete":::

## <a name="save-use-and-delete-views"></a>Nézetek mentése, használata és törlése

Mentheti a kiválasztott szűrőket és oszlopokat tartalmazó nézeteket is. Nézet mentése és használata:

1. Válassza a **kezelés nézet** lehetőséget, majd **mentse a nézetet**.

1. Adja meg a nézet nevét, majd kattintson **az OK gombra**. A mentett nézet most megjelenik a **kezelés nézet** menüben.

    :::image type="content" source="media/manage-filter-resource-views/simple-view.png" alt-text="Az összes erőforrás Azure Portal nézete" panel mentett nézeteiben** válassza ki a nézetet, majd válassza a **Törlés** ikon ![ Törlés nézet ikont ](media/manage-filter-resource-views/icon-delete.png) .

## <a name="summarize-resources-with-visuals"></a>Erőforrások összefoglalása vizualizációkkal

Az eddig megtekintett nézetek _listáját_láthatjuk, de vannak olyan _összegző nézetek_ is, amelyek vizualizációkat tartalmaznak. Ezeket a nézeteket mentheti és használhatja, ahogyan a nézeteket is listázhatja. A szűrők a két típusú nézet között maradnak. A szokásos nézetek, például az alább látható **hely** nézet, valamint az adott szolgáltatásokhoz kapcsolódó nézetek, például a virtuális gépek **állapot** nézete.

:::image type="content" source="media/manage-filter-resource-views/summary-map.png" alt-text="Az összes erőforrás Azure Portal nézete":::

Egy összegző nézet mentéséhez és használatához:

1. A Nézet menüben válassza az **összesítő nézet**lehetőséget.

    :::image type="content" source="media/manage-filter-resource-views/menu-summary-view.png" alt-text="Az összes erőforrás Azure Portal nézete":::

1. Az összegző nézet lehetővé teszi különböző attribútumok, például a **hely** és a **típus**összefoglalását. Válassza ki az **Összefoglalás** lehetőséget és a megfelelő vizualizációt. Az alábbi képernyőfelvételen látható a **típus összefoglalása** egy **oszlopdiagram** vizualizációval.

    :::image type="content" source="media/manage-filter-resource-views/type-summary-bar-chart.png" alt-text="Az összes erőforrás Azure Portal nézete":::

1. Válassza a **kezelés nézet** , majd a **Mentés** lehetőséget a nézet mentéséhez, ahogy azt a listanézet is tette.

1. Az összefoglalás nézetben a **típus összegzése**területen jelöljön ki egy sávot a diagramon. A sáv kiválasztása esetén a lista egyetlen típusú erőforrásra van leszűrve.

    :::image type="content" source="media/manage-filter-resource-views/all-resources-filtered-type.png" alt-text="Az összes erőforrás Azure Portal nézete":::

## <a name="run-queries-in-azure-resource-graph"></a>Lekérdezések futtatása az Azure Resource Graph-ban

Az Azure Resource Graph hatékony és teljesítménybeli erőforrás-feltárást biztosít az előfizetések széles skálájának lekérdezéséhez. A Azure Portal **minden erőforrás** képernyőjén szerepel egy hivatkozás, amely az aktuális szűrt nézetre hatókörrel rendelkező Resource Graph-lekérdezést nyit meg.

Erőforrás-gráf lekérdezésének futtatása:

1. Válassza a **lekérdezés megnyitása**lehetőséget.

    :::image type="content" source="media/manage-filter-resource-views/open-query.png" alt-text="Az összes erőforrás Azure Portal nézete":::

1. Az **Azure Resource Graph Explorerben**válassza a **lekérdezés futtatása** lehetőséget az eredmények megtekintéséhez.

    :::image type="content" source="media/manage-filter-resource-views/run-query.png" alt-text="Az összes erőforrás Azure Portal nézete":::

    További információ: [az első Resource Graph-lekérdezés futtatása az Azure Resource Graph Explorerben](../governance/resource-graph/first-query-portal.md).

## <a name="next-steps"></a>További lépések

[Az Azure Portal áttekintése](azure-portal-overview.md)

[Irányítópultok létrehozása és megosztása az Azure Portalon](azure-portal-dashboards.md)
