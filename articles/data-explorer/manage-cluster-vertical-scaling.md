---
title: Egy változó igényeket elégíthet Azure adatkezelő fürt vertikális
description: Ez a cikk ismerteti a lépéseket vertikális felskálázás és vertikális leskálázás egy igény szerinti rendelheti az adatkezelőt az Azure-fürtön.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: dc9ca8bb592e699d19835efeafb91e81408ae297
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571529"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>Fürt vertikális felskálázása változó igényeket elégíthet kezelése

Van egy Azure Data Explorer-fürt méretezése két munkafolyamatok:
1. [Horizontális skálázás](manage-cluster-horizontal-scaling.md), más néven is néven horizontális fel- és a.
2. Függőleges méretezés, más néven felfelé és lefelé skálázást.

Ez a cikk bemutatja, hogyan kezelheti a fürt vertikális skálázás.

Fürt méretezése megfelelően, kritikus fontosságú a teljesítmény az Azure Data Explorer. De igény szerint egy fürtön nem becsülhető abszolút pontossággal. Egy statikus fürtméret vezethet alulkihasználtságának vagy overutilization, ezek egyike sem nem ideális. Az, hogy esetleg jobb megközelítés *méretezési* egy fürthöz, hozzáadása és eltávolítása a kapacitás és a Processzor-erőforrások igény szerinti módosítását. 

## <a name="steps-to-configure-vertical-scaling"></a>Vertikális skálázás konfigurálásának lépései

1. Nyissa meg a fürt. A **beállítások**válassza **vertikális felskálázás**.

    A rendelkezésre álló termékváltozatok listáját még látható. Például az alábbi ábrán csak négy termékváltozatban érhetők el.

    ![Vertikális felskálázás](media/manage-cluster-vertical-scaling/scale-up.png)

    SKU-k le vannak tiltva, mert az aktuális Termékváltozat zajlik, vagy nem érhetők el a régióban, ahol a fürt is található.

1. A Termékváltozat, jelölje ki a Termékváltozat szeretne, majd válassza ki a **kiválasztása** gombra.

> [!NOTE]
> A vertikális skálázása folyamat néhány percet is igénybe vehet, és ebben az időszakban a fürt felfüggesztjük. Vegye figyelembe, hogy a vertikális leskálázást károsíthatják a fürt teljesítményét.

Most végezze el az Azure Data Explorer fürt egy felfelé vagy lefelé művelet.

Ha a fürt méretezése problémák segítségre van szüksége [nyisson egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) az Azure Portalon.

## <a name="next-steps"></a>További lépések

* [Fürt horizontális méretezés kezelésére](manage-cluster-horizontal-scaling.md) dinamikusan méretezheti az out a példányok száma a megadott metrikák alapján.

* Az erőforrás-használat figyelése a következő cikkben: [Azure Data Explorer Teljesítményfigyelő, egészségügyi és használati metrikákkal](using-metrics.md).

