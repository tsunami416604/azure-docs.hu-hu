---
title: Adatok törlése az Azure Data Explorer
description: Ez a cikk ismerteti a tömeges törlés alkalmazási helyzeteket az Azure adatok megismerése, beleértve a végleges törlése, és törli az adatmegőrzési-alapú.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 571a005dd3f50690f291a7ffa3c1174ea15cb0ed
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47047610"
---
# <a name="delete-data-from-azure-data-explorer"></a>Adatok törlése az Azure Data Explorer

Az adatkezelő az Azure támogatja a több tömeges törlés megközelítést, amely ebben a cikkben bemutatjuk. Azt nem támogatja a rekord törlési valós időben, mert gyors olvasási hozzáférés van optimalizálva.

* Ha az adatbázis már nincs rá szükség, törölje azt a drop database parancs használatával.

    ```Kusto
    .drop database <DatabaseName>
    ```

* Egy vagy több tábla már nincs rá szükség, ha törli ezeket a drop table segítségével, vagy dobja el a táblák parancs.

    ```Kusto
    .drop table <TableName>

    .drop tables (<TableName1>, <TableName2>,...)
    ```

* Ha régi adatokat már nincs rá szükség, törölje az adatbázis vagy táblázat szintjén a megőrzési időszak módosításával.

    Fontolja meg az adatbázis vagy táblázat, amely a 90 napos adatmegőrzést. Üzleti kell változik, hogy most csak 60 nap adatait van szükség. Ebben az esetben törölje a régebbi adatokat az alábbi módszerek egyikével.

    ```Kusto
    .alter-merge database <DatabaseName> policy retention softdelete = 60d

    .alter-merge table <TableName> policy retention softdelete = 60d
    ```

    További információkért lásd: [adatmegőrzési](https://docs.microsoft.com/azure/kusto/concepts/retentionpolicy).

* Az egyes rekordok használatával törölheti az *kiürítése* művelet, egy predikátum hasonló alapján `where CustomerName == 'contoso'`. Mindemellett végleges törlésére a valós idejű törlése nem tervezett tömeges törlés. Az alábbi példa bemutatja a végleges törlésére.

    ```Kusto
    .purge table Customer records
    | where CustomerName =='contoso'
    ```

Ha adat törlésének problémák segítségre van szüksége, nyisson egy támogatási kérést a [az Azure portal](https://portal.azure.com).