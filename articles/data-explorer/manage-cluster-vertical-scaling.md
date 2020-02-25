---
title: A fürt függőleges skálázásának kezelése (vertikális felskálázás) az Azure-Adatkezelő igényének megfelelően
description: Ez a cikk az Azure Adatkezelő-fürt vertikális felskálázásának és méretezésének lépéseit ismerteti az igények módosítása alapján.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 95275598febae2b6b0355a7bc3e512490dae500d
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2020
ms.locfileid: "77560439"
---
# <a name="manage-cluster-vertical-scaling-scale-up-in-azure-data-explorer-to-accommodate-changing-demand"></a>A fürt vertikális skálázása (horizontális felskálázás) kezelése az Azure Adatkezelő a változó igények kielégítése érdekében

A fürt megfelelő méretezése az Azure-Adatkezelő teljesítményének szempontjából kritikus fontosságú. A statikus fürt mérete a használaton kívüli vagy túlzott kihasználtságot eredményezhet, ami egyik ideális megoldás.

Mivel a fürt iránti igényt nem lehet abszolút pontossággal előre jelezni, a jobb megoldás a fürt *méretezése* , a kapacitás és a CPU-erőforrások hozzáadása és eltávolítása a változó igényekkel. 

Az Azure Adatkezelő-fürtök méretezésére két munkafolyamat áll rendelkezésre:

* [Horizontális skálázás](manage-cluster-horizontal-scaling.md), más néven skálázás be-és kifelé.
* Vertikális skálázás, más néven felfelé és lefelé skálázás.

Ez a cikk a vertikális skálázási munkafolyamatot ismerteti:

## <a name="configure-vertical-scaling"></a>Vertikális skálázás konfigurálása

1. A Azure Portal lépjen az Azure Adatkezelő fürterőforrás elemre. A **Beállítások**területen válassza a vertikális **felskálázás**lehetőséget.

1. A **felskálázás** ablakban megjelenik a fürthöz elérhető SKU-ket tartalmazó lista. Az alábbi ábrán például csak négy SKU érhető el.

    ![Vertikális felskálázás](media/manage-cluster-vertical-scaling/scale-up.png)

    Az SKU-ket a rendszer letiltotta, mert az aktuális SKU, vagy nem érhetők el abban a régióban, ahol a fürt található.

1. Az SKU módosításához válasszon ki egy új SKU-t, majd kattintson a **kiválasztás**elemre.

> [!NOTE]
> * A vertikális skálázási folyamat eltarthat néhány percig, és ez idő alatt a fürt fel lesz függesztve. 
> * A leskálázás a fürt teljesítményének károsodását okozhatja.
> * Az ár a fürt virtuális gépei és az Azure Adatkezelő szolgáltatási költségeinek becslése. Egyéb költségeket nem tartalmaz. A teljes díjszabási információkért tekintse meg az Azure Adatkezelő a [Cost kalkulátort](https://dataexplorer.azure.com/AzureDataExplorerCostEstimator.html) ismertető oldalt, valamint az Azure adatkezelő [díjszabási oldalát](https://azure.microsoft.com/pricing/details/data-explorer/) .

Most beállította a vertikális skálázást az Azure Adatkezelő-fürthöz. Adjon hozzá egy másik szabályt egy horizontális skálázáshoz. Ha segítségre van szüksége a fürtök skálázásával kapcsolatos problémák megoldásához, [Nyisson meg egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) a Azure Portal.

## <a name="next-steps"></a>További lépések

* A [fürt vízszintes skálázásának kezelése](manage-cluster-horizontal-scaling.md) a megadott mérőszámok alapján a példányszám dinamikusan horizontális felskálázásához.

* Figyelje meg az erőforrás-használatot a következő cikk alapján: az [Azure adatkezelő teljesítményének, állapotának és használatának mérőszámokkal történő figyelése](using-metrics.md).

