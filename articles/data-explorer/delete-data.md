---
title: Adatok törlése az Azure Data Explorer
description: Ez a cikk ismerteti a tömeges törlés alkalmazási helyzeteket az Azure adatok megismerése, beleértve a végleges törlése, és törli az adatmegőrzési-alapú.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 48336b424ef1783d8eb123ac45bfc03b98d5481b
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756983"
---
# <a name="delete-data-from-azure-data-explorer"></a>Adatok törlése az Azure Data Explorer

Az adatkezelő az Azure támogatja a több tömeges törlés megközelítést, amely ebben a cikkben bemutatjuk. Azt nem támogatja a rekord törlési valós időben, mert gyors olvasási hozzáférés van optimalizálva.

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

Ha adat törlésének problémák segítségre van szüksége, nyisson egy támogatási kérést a [az Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
