---
title: 'Rövid útmutató: Az első portállekérdezés'
description: Ebben a rövid útmutatóban kövesse az első lekérdezés futtatásához az Azure Resource Graph Explorer használatával az első lekérdezés futtatásához.
ms.date: 11/21/2019
ms.topic: quickstart
ms.openlocfilehash: 5cf355e78ad51e06d7ba27d48dd352f35b4c0740
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "74406799"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-resource-graph-explorer"></a>Rövid útmutató: Az első Resource Graph-lekérdezés futtatása az Azure Resource Graph Explorer használatával

Az Azure Resource Graph teljesítménye közvetlenül az Azure Resource Graph Exploreren keresztül érhető el az Azure Resource Graph Exploreren keresztül. A Resource Graph Explorer böngészhető információkat nyújt az Azure Resource Manager erőforrástípusairól és tulajdonságairól, amelyek lekérdezhetők. A Resource Graph Explorer egy tiszta felületet is biztosít a több lekérdezéssel való munkához, az eredmények kiértékeléséhez, és néhány lekérdezés eredményének egy Azure-irányítópulthoz rögzíthető diagramba történő konvertálásához.

A rövid útmutató végén az Azure Portal és a Resource Graph Explorer használatával futtatta az első Resource Graph-lekérdezést, és rögzítette az eredményeket egy irányítópulton.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot, mielőtt elkezdené.

## <a name="run-your-first-resource-graph-query"></a>Az első Resource Graph-lekérdezés futtatása

Nyissa meg az [Azure Portalt](https://portal.azure.com) az Erőforrásgráf-kezelő megkereséséhez és használatához az alábbi lépések végrehajtásához az első Resource Graph-lekérdezés futtatásához:

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza az **Erőforrásgráf-kezelőt.**

1. Az ablak **Lekérdezés 1** részében írja `Resources | project name, type | limit 5` be a lekérdezést, és válassza a **Lekérdezés futtatása**lehetőséget.

   > [!NOTE]
   > Mivel ez a lekérdezési példa nem biztosít `order by`rendezésmódosítót, például a lekérdezés többszöri futtatásával valószínűleg kérésenként más erőforráskészletet eredményez.

1. Tekintse át a lekérdezésválaszát az **Messages** **Eredmények** lapon. A hibák ( ha vannak ) ezen a lapon jelennek meg.

1. Frissítse a `order by` lekérdezést a `Resources | project name, type | limit 5 | order by name asc` **Name** tulajdonságra: . Ezután válassza a **Lekérdezés futtatása**lehetőséget.

   > [!NOTE]
   > Csakúgy, mint az első lekérdezésnél, e lekérdezés többszöri futtatása esetén is valószínűleg minden kéréssel eltérő erőforráslistát fog kapni. Fontos a lekérdezési parancsok sorrendje. Ebben a példában az `order by` a `limit` után következik. Így először korlátozza a lekérdezés eredményeit, majd rendezi őket.

1. Frissítse `order by` a lekérdezést először a **Name** tulajdonságra, majd `limit` az első öt eredményre: `Resources | project name, type | order by name asc | limit 5`. Ezután válassza a **Lekérdezés futtatása**lehetőséget.

Ha a végső lekérdezés többször fut, feltételezve, hogy a környezetben semmi sem változik, a visszaadott eredmények konzisztensek és a várt módon – a **Name** tulajdonság szerint rendezve, de továbbra is csak az első öt eredményre korlátozódik.

### <a name="schema-browser"></a>Sémaböngésző

A sémaböngésző az Erőforrásgráf-kezelő bal oldali ablaktáblájában található. Ez az erőforrások listája az Azure-erőforrások összes _erőforrástípusát_ jeleníti meg, amelyeket az Azure Resource Graph támogat, és amelyek egy olyan bérlőben találhatók, amelyhez hozzáféréssel rendelkezik. Az erőforrástípus vagy altulajdonságok kibontása olyan alárendelt tulajdonságokat jelenít meg, amelyek az Erőforrásgráf-lekérdezés létrehozásához használhatók.

Az erőforrástípus `where type =="<resource type>"` kiválasztása a lekérdezés mezőben található. Az alárendelt tulajdonságok `where <propertyName> == "INSERT_VALUE_HERE"` egyikének kijelölése hozzáadódik a lekérdezési mezőbe.
A sémaböngésző nagyszerű módja a lekérdezésekben használható tulajdonságok felderítésének. Ügyeljen arra, hogy _az INSERT VALUE ITT-t\_\__ a saját értékére cserélje, állítsa be a lekérdezést feltételekkel, operátorokkal és függvényekkel a kívánt eredmények elérése érdekében.

## <a name="create-a-chart-from-the-resource-graph-query"></a>Diagram létrehozása az Erőforrásgráf lekérdezésből

A fenti utolsó lekérdezés futtatása után, ha a Diagramok lapot **választja,** egy üzenet jelenik meg, hogy "az eredményhalmaz nem kompatibilis a kördiagram-megjelenítéssel". Az eredményeket tartalmazó lekérdezések nem használhatók diagramba, de az erőforrások számát biztosító lekérdezések nem. A [minta lekérdezés használatával – Virtuális gépek megszámlálása operációs rendszer típusa szerint,](./samples/starter.md#count-virtual-machines-by-os-type)hozzon létre egy vizualizációt az Erőforrásgráf lekérdezésből.

1. Az ablak **Lekérdezés 1** részében írja be a következő lekérdezést, és válassza a **Lekérdezés futtatása**lehetőséget.

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

1. Válassza az **Eredmények lapot,** és vegye figyelembe, hogy a lekérdezésre adott válasz számot ad.

1. Válassza a **Diagramok** lapot. Most a lekérdezés vizualizációkat eredményez. Módosítsa a szöveget _a Diagramtípus kiválasztása ..._ a _Sávdiagram_ vagy _a Fánk diagramra,_ hogy kísérletezzen a rendelkezésre álló vizualizációs beállításokkal.

## <a name="pin-the-query-visualization-to-a-dashboard"></a>A lekérdezési vizualizáció rögzítése irányítópulton

Ha egy vizualizálható lekérdezés eredményei vannak, akkor az adatvizualizáció rögzíthető az egyik irányítópultra. A fenti lekérdezés futtatása után kövesse az alábbi lépéseket:

1. Válassza a **Mentés lehetőséget,** és adja meg a "Virtuális gépek operációs rendszer típusa szerint" nevet. Ezután válassza a **Mentés** gombot a jobb oldali ablaktábla alján.

1. Válassza **a Lekérdezés futtatása lehetőséget** a lekérdezés mentésének mosti újrafuttatásához.

1. A **Diagramok** lapon jelöljön ki egy adatmegjelenítést. Ezután válassza **a Rögzítés az irányítópultra**lehetőséget.

1. Válassza ki a megjelenő portálértesítést, vagy válassza az **Irányítópult** lehetőséget a bal oldali ablaktáblából.

A lekérdezés most már elérhető az irányítópulton a lekérdezés nevének megfelelő csempe címével. Ha a lekérdezés nem lett mentve a rögzítéskor, akkor helyette "Query 1" lesz.

A lekérdezés és az eredményül kapott adatvizualizáció minden alkalommal fut és frissül, amikor az irányítópult betöltődik, és valós idejű és dinamikus elemzéseket biztosít az Azure-környezetbe közvetlenül a munkafolyamatban.

> [!NOTE]
> A listát eredményező lekérdezések az irányítópulton is rögzíthetők. A szolgáltatás nem korlátozódik a lekérdezések adatvizualizációira.

## <a name="import-example-resource-graph-explorer-dashboards"></a>Példák Resource Graph Explorer-irányítópultjainak importálása

Példák at Resource Graph lekérdezések és hogyan ErőforrásGráf-kezelő használható az Azure Portal munkafolyamat, próbálja ki ezeket a példa irányítópultok.

- [Erőforrás-grafikon-kezelő – Mintairányítópult #1](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-1/resourcegraphexplorer-sample-1.json)

  [![Példa kép a mintairányítópult #1](./media/arge-sample1-small.png)](./media/arge-sample1-large.png#lightbox)

- [Erőforrás-grafikon-kezelő – Mintairányítópult #2](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-2/resourcegraphexplorer-sample-2.json)

  [![Példa kép a mintairányítópult #2](./media/arge-sample2-small.png)](./media/arge-sample2-large.png#lightbox)

> [!NOTE]
> A fenti példában az irányítópult képernyőképei az Azure-környezettől függően változnak.

1. Jelölje ki és töltse le a kiértékelni kívánt mintairányítópultot.

1. Az Azure Portalon válassza **az Irányítópult lehetőséget** a bal oldali ablaktáblából.

1. Válassza **a Feltöltés**lehetőséget, majd keresse meg és jelölje ki a letöltött irányítópult-fájlt. Ezután válassza **a Megnyitás**lehetőséget.

Az importált irányítópult automatikusan megjelenik. Mivel most már létezik az Azure Portalon, szükség szerint megvizsgálhatja és módosíthatja a módosításokat, vagy új irányítópultokat hozhat létre a példából, hogy megossza a csapataival. Az irányítópultok használatával kapcsolatos további tudnivalókért olvassa [el az Irányítópultok létrehozása és megosztása az Azure Portalon című témakört.](../../azure-portal/azure-portal-dashboards.md)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha el szeretné távolítani a minta Resource Graph irányítópultokat az Azure Portal környezetből, ezt a következő lépésekkel teheti meg:

1. Válassza az **Irányítópult** elemet a bal oldali ablaktáblából.

1. Az irányítópult legördülő menüből válassza ki a törölni kívánt Erőforrásgráf irányítópult-mintát.

1. Válassza a **Törlés** parancsot az irányítópult tetején található irányítópult menüből, majd a megerősítéshez válassza az **Ok** gombot.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban az Azure Resource Graph Explorer használatával futtatta az első lekérdezést, és megtekintette az Erőforrás-diagramra épülő irányítópult-példákat. Ha többet szeretne megtudni az Erőforrás-diagram nyelvéről, folytassa a lekérdezésnyelv részletei lappal.

> [!div class="nextstepaction"]
> [További információ a lekérdezés nyelvéről](./concepts/query-language.md)