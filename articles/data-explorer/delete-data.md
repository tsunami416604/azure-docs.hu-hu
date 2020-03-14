---
title: Adatok törlése az Azure Adatkezelő
description: Ez a cikk az Azure Adatkezelő törlési forgatókönyveit ismerteti, beleértve a kiürítést, az eldobási egységeket és a megőrzési alapú törléseket.
author: orspod
ms.author: orspodek
ms.reviewer: avneraa
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 681cfd71d2666630b192935d66ba32eaf16c92de
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204613"
---
# <a name="delete-data-from-azure-data-explorer"></a>Adatok törlése az Azure Adatkezelő

Az Azure Adatkezelő a jelen cikkben ismertetett különféle törlési forgatókönyveket támogatja. 

## <a name="delete-data-using-the-retention-policy"></a>Adatok törlése az adatmegőrzési szabály használatával

Az Azure Adatkezelő automatikusan törli az [adatmegőrzési házirend](/azure/kusto/management/retentionpolicy)alapján tárolt adatokkal. Ez a módszer az adattörlés leghatékonyabb és problémamentes módja. Állítsa be az adatmegőrzési szabályt az adatbázis vagy a tábla szintjén.

Vegye fontolóra egy olyan adatbázist vagy táblázatot, amely 90 napos megőrzési időtartamra van beállítva. Ha csak 60 napos adatmennyiségre van szükség, törölje a régebbieket a következő módon:

```kusto
.alter-merge database <DatabaseName> policy retention softdelete = 60d

.alter-merge table <TableName> policy retention softdelete = 60d
```

## <a name="delete-data-by-dropping-extents"></a>Az adattörlési egységek törlése

A [egység (adatszegmens)](/azure/kusto/management/extents-overview) az a belső struktúra, amelyben az adat tárolása történik. Minden egység akár több millió rekordot is képes tárolni. A mértékek egyenként, vagy a [drop mérték (ek) parancsok](/azure/kusto/management/extents-commands#drop-extents)használatával törölhetők. 

### <a name="examples"></a>Példák

Törölheti egy tábla összes sorát, vagy csak egy adott mértékét.

* Tábla összes sorának törlése:

    ```kusto
    .drop extents from TestTable
    ```

* Adott mérték törlése:

    ```kusto
    .drop extent e9fac0d2-b6d5-4ce3-bdb4-dea052d13b42
    ```

## <a name="delete-individual-rows-using-purge"></a>Egyes sorok törlése a kiürítés használatával

Az [adattörlést](/azure/kusto/management/data-purge) egyéni sorok törlésére is használhatja. A törlés nem azonnali, és jelentős rendszererőforrásokat igényel. Ezért csak a megfelelőségi forgatókönyvek esetében ajánlott.  

