---
title: Egy változó igényeket elégíthet Azure adatkezelő fürt vertikális
description: Ez a cikk ismerteti a lépéseket vertikális felskálázás és vertikális leskálázás egy igény szerinti rendelheti az adatkezelőt az Azure-fürtön.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/05/2019
ms.openlocfilehash: 1f130f79b6b6924559e1693e1eef8ced2972b3d5
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59279397"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>Fürt vertikális felskálázása változó igényeket elégíthet kezelése

Az Azure Data Explorer fürt méretezése két munkafolyamatok: vertikális felskálázás és [kibővített](manage-cluster-scale-out.md). Ez a cikk bemutatja, hogyan kezelheti a fürt vertikális felskálázása.

Fürt méretezése megfelelően, kritikus fontosságú a teljesítmény az Azure Data Explorer. De igény szerint egy fürtön nem becsülhető abszolút pontossággal. Egy statikus fürtméret vezethet alulkihasználtságának vagy overutilization, ezek egyike sem nem ideális. Az, hogy esetleg jobb megközelítés *méretezési* egy fürthöz, hozzáadása és eltávolítása a kapacitás és a Processzor-erőforrások igény szerinti módosítását. 

## <a name="steps-to-scale-up"></a>Vertikális felskálázás lépései
1. Nyissa meg a fürt. A **beállítások**válassza **vertikális felskálázás**.

    A rendelkezésre álló termékváltozatok listáját még látható. Például az alábbi ábrán csak négy termékváltozatban érhetők el.

    ![Vertikális felskálázás](media/manage-cluster-scale-up/scale-up.png)

    SKU-k le vannak tiltva, mert az aktuális Termékváltozat zajlik, vagy nem érhetők el a régióban, ahol a fürt is található.

1. A Termékváltozat, jelölje ki a Termékváltozat szeretne, majd válassza ki a **kiválasztása** gombra.

> [!NOTE]
> A vertikális felskálázás folyamat néhány percet is igénybe vehet, és ebben az időszakban a fürt felfüggesztjük. Vegye figyelembe, hogy a vertikális leskálázást károsíthatják a fürt teljesítményét.

Most végezze el az Azure Data Explorer fürt egy felfelé vagy lefelé művelet.

## <a name="next-steps"></a>További lépések
Emellett [kezelheti a fürt kibővített](manage-cluster-scale-out.md) ki a példányok száma a megadott metrikák alapján dinamikusan méretezheti.

Ha a fürt méretezése problémák segítségre van szüksége [nyisson egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) az Azure Portalon.

Az erőforrás-használat figyelése a következő cikkben: [Azure Data Explorer Teljesítményfigyelő, egészségügyi és használati metrikákkal](using-metrics.md).
