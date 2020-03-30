---
title: Adatok törlése az Azure Data Explorerből
description: Ez a cikk ismerteti a törlési forgatókönyvek az Azure Data Explorer, beleértve a kiürítési, eldobási terjedelem és megőrzési alapú törlések.
author: orspod
ms.author: orspodek
ms.reviewer: avneraa
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: dd0f8740d148a7817bcfe2fbad591ceeb1610d0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501410"
---
# <a name="delete-data-from-azure-data-explorer"></a>Adatok törlése az Azure Data Explorerből

Az Azure Data Explorer támogatja a jelen cikkben ismertetett különböző törlési forgatókönyveket. 

## <a name="delete-data-using-the-retention-policy"></a>Adatok törlése az adatmegőrzési házirend használatával

Az Azure Data Explorer automatikusan törli az adatokat az [adatmegőrzési szabályzat](/azure/kusto/management/retentionpolicy)alapján. Ez a módszer az adatok törlésének leghatékonyabb és leggondmentesebb módja. Állítsa be az adatmegőrzési szabályt az adatbázis vagy a tábla szintjén.

Fontolja meg egy 90 napos megőrzésre beállított adatbázist vagy táblát. Ha csak 60 nap adatra van szükség, törölje a régebbi adatokat az alábbiak szerint:

```kusto
.alter-merge database <DatabaseName> policy retention softdelete = 60d

.alter-merge table <TableName> policy retention softdelete = 60d
```

## <a name="delete-data-by-dropping-extents"></a>Adatok törlése a terjedelem eldobásával

[A terjedelem (adatszegmens)](/azure/kusto/management/extents-overview) az adatok tárolási helye szerinti belső struktúra. Minden egyes mérték több millió rekordot képes tárolni. A terjedelmek egyenként vagy csoportként törölhetők [a ledobási kiterjedés(ek) parancsaival.](/azure/kusto/management/extents-commands#drop-extents) 

### <a name="examples"></a>Példák

Törölheti a tábla összes sorát, vagy csak egy adott mértékben.

* A tábla összes sorának törlése:

    ```kusto
    .drop extents from TestTable
    ```

* Adott mérték törlése:

    ```kusto
    .drop extent e9fac0d2-b6d5-4ce3-bdb4-dea052d13b42
    ```

## <a name="delete-individual-rows-using-purge"></a>Egyes sorok törlése a kiürítés használatával

[Az adatok törlése](/azure/kusto/concepts/data-purge) egyének sorainak törlésére használható. A törlés nem azonnali, és jelentős rendszererőforrásokat igényel. Mint ilyen, ez csak megfelelőségi forgatókönyvek ajánlott.  

