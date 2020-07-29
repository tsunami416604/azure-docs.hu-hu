---
title: ALTER EXTERNAL STREAM (Transact-SQL) – Azure SQL Edge (előzetes verzió)
description: Ismerje meg az ALTER EXTERNAL STREAM utasítást az Azure SQL Edge-ben (előzetes verzió)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 2559c4b4b875403b7c70671e27cb6222a3f1103a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84235202"
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


## <a name="see-also"></a>Lásd még

- [KÜLSŐ STREAM létrehozása (Transact-SQL)](create-external-stream-transact-sql.md) 
- [KÜLSŐ STREAM eldobása (Transact-SQL)](drop-external-stream-transact-sql.md) 
