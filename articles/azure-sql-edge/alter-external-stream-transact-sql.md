---
title: ALTER EXTERNAL STREAM (Transact-SQL) – Azure SQL Edge (előzetes verzió)
description: Ismerje meg az ALTER EXTERNAL STREAM utasítást az Azure SQL Edge-ben (előzetes verzió)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 0183972b5eb92d3f081b857940609bffc183b331
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597939"
---
# <a name="alter-external-stream-transact-sql"></a>KÜLSŐ STREAM módosítása (Transact-SQL)

Módosítja egy külső adatfolyam definícióját. A folyamatos átviteli feladatok által a *futó* állapotokban használt külső adatfolyamok módosítása nem engedélyezett. 



## <a name="syntax"></a>Szintaxis

```sql
  ALTER EXTERNAL STREAM external_stream_name 
  SET 
    [DATA_SOURCE] = <data_source_name> 
    , LOCATION = <location_name> 
    , EXTERNAL_FILE_FORMAT = <external_file_format_name> 
    , INPUT_OPTIONS = <input_options> 
    , OUTPUT_OPTIONS = <output_options> 
```

## <a name="arguments"></a>Argumentumok

További információ az Alter External stream parancs argumentumáról: [külső stream létrehozása (Transact-SQL)](create-external-stream-transact-sql.md).

## <a name="return-code-values"></a>Visszatérési kód értékei

Ha a művelet sikeres, a külső STREAM módosítása 0 értéket ad vissza. A nullától eltérő visszatérési érték azt jelzi, hogy hiba történt.


## <a name="see-also"></a>További információ

- [KÜLSŐ STREAM létrehozása (Transact-SQL)](create-external-stream-transact-sql.md) 
- [KÜLSŐ STREAM eldobása (Transact-SQL)](drop-external-stream-transact-sql.md) 
