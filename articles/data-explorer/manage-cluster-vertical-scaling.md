---
title: Fürt vertikális skálázásának kezelése (vertikális felskálázás) az Azure Data Explorer ben az igényeknek megfelelően
description: Ez a cikk az Azure Data Explorer-fürt igény szerinti skálázására és leskálázására szolgáló lépéseket ismerteti.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 95275598febae2b6b0355a7bc3e512490dae500d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560439"
---
# <a name="manage-cluster-vertical-scaling-scale-up-in-azure-data-explorer-to-accommodate-changing-demand"></a>Fürt vertikális skálázásának kezelése (vertikális felskálázás) az Azure Data Explorerben a változó igényeknek megfelelően

A fürt megfelelő méretezése kritikus fontosságú az Azure Data Explorer teljesítménye szempontjából. A statikus fürtméret kihasználatlan vagy túlkihasználtsághoz vezethet, amelyek közül egyik sem ideális.

Mivel a fürt igénye nem előre jelezhető abszolút pontossággal, jobb megközelítés a fürt *méretezése,* a kapacitás és a CPU-erőforrások hozzáadása és eltávolítása a változó igényekkel. 

Az Azure Data Explorer-fürt méretezéséhez két munkafolyamat létezik:

* [Horizontális skálázás](manage-cluster-horizontal-scaling.md), más néven méretezés be- és kiméretezés.
* Függőleges skálázás, más néven felskálázás és leskálázás.

Ez a cikk a vertikális méretezési munkafolyamatot ismerteti:

## <a name="configure-vertical-scaling"></a>Függőleges méretezés konfigurálása

1. Az Azure Portalon nyissa meg az Azure Data Explorer fürterőforrás. A **Beállítások csoportban**válassza a **Felskálázás**lehetőséget.

1. A **Felskálázás** ablakban megjelenik a fürthöz rendelkezésre álló sk-ek listája. Az alábbi ábrán például csak négy ska érhető el.

    ![Vertikális felskálázás](media/manage-cluster-vertical-scaling/scale-up.png)

    A termékváltozatok le vannak tiltva, mert ők az aktuális termékváltozat, vagy nem érhetők el a régióban, ahol a fürt található.

1. A termékváltozat módosításához jelöljön ki egy új termékváltozatot, és kattintson **a Kijelölés gombra.**

> [!NOTE]
> * A függőleges skálázási folyamat eltarthat néhány percig, és ez idő alatt a fürt felfüggesztésre kerül. 
> * A leskálázás károsíthatja a fürt teljesítményét.
> * Az ár a fürt virtuális gépeinek és az Azure Data Explorer szolgáltatás költségeinek becslése. Az egyéb költségeket nem tartalmazza. Tekintse meg az Azure Data Explorer [költségbecslési](https://dataexplorer.azure.com/AzureDataExplorerCostEstimator.html) lapján egy becslést, és az Azure Data Explorer [díjszabási oldalon](https://azure.microsoft.com/pricing/details/data-explorer/) a teljes díjszabási információkat.

Most már konfigurálta a függőleges skálázást az Azure Data Explorer-fürthöz. Adjon hozzá egy másik szabályt egy vízszintes méretezéshez. Ha segítségre van szüksége a fürtskálázási problémák, [nyisson meg egy támogatási kérelmet](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) az Azure Portalon.

## <a name="next-steps"></a>További lépések

* [Fürt vízszintes skálázás kezelése](manage-cluster-horizontal-scaling.md) a példányszám dinamikus horizontális skálázása a megadott metrikák alapján.

* Az erőforrás-használat figyelése a következő cikkben: [Az Azure Data Explorer teljesítményének, állapotának és használatának figyelése metrikákkal.](using-metrics.md)

