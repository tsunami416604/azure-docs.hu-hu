---
title: sys. external_job_streams (Transact-SQL) – Azure SQL Edge (előzetes verzió)
description: Tudnivalók a sys. external_job_streams használatáról az Azure SQL Edge-ben (előzetes verzió)
keywords: sys. external_job_streams, SQL Edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: 7f26c87c0de09618b2d24413f7ff692fd764b4cf
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597288"
---
# <a name="sysexternal_job_streams-transact-sql"></a>sys. external_job_streams (Transact-SQL)

Egy olyan sort ad vissza, amely a külső adatfolyam-feladathoz hozzárendelt bemeneti vagy kimeneti külső stream objektumra vonatkozik.

|Oszlop neve|Adattípus|Leírás|  
|-----------------|---------------|-----------------|
|**job_id**|**int**| A folyamatos átviteli feladattípushoz tartozó objektum-azonosító szám. Ez az oszlop a sys. external_streaming_jobs object_id oszlopát képezi le.|
|**stream_id**|**int**| Az adatfolyam-objektumhoz tartozó objektum-azonosító szám. Ez az oszlop a sys. external_streams object_id oszlopát képezi le. |
|**is_input**|**bit**| 1 Ha a Stream-objektum bemenetet használ a folyamatos átviteli feladathoz, ellenkező esetben 0.|
|**is_output**|**bit**| 1 Ha a Stream objektum kimenetet használ a folyamatos átviteli feladatokhoz, ellenkező esetben 0.|

## <a name="example"></a>Példa

Ez a katalógus-nézet a `sys.external_streams` és a `sys.external_streaming_jobs` katalógus nézeteivel együtt használható. Alább látható egy minta lekérdezés

```sql
Select
    sj.Name as Job_Name,
    sj.Create_date as Job_Create_date,
    sj.modify_date as Job_Modify_date,
    sj.statement as Stream_Job_Query,
    Input_Stream_Name =
        Case js.is_input
            when 1 then s.Name
            else null
        END,
    output_Stream_Name =
        case js.is_output
            when 1 then s.Name
            else null
        END,
    s.location as Stream_Location
from sys.external_job_streams js
inner join sys.external_streams s on s.object_id = js.stream_id
inner join sys.external_streaming_jobs sj on sj.object_id = js.job_id
```

## <a name="permissions"></a>Engedélyek

A metaadatoknak a katalógus nézeteiben való láthatósága azon biztonságos elemek migrálására korlátozódik, amelyeknek a felhasználó tulajdonában van, vagy amelyeken a felhasználó engedélyt kapott. További információ: [metaadatok láthatóságának konfigurálása](/sql/relational-databases/security/metadata-visibility-configuration/).

## <a name="see-also"></a>További információ

- [Katalógus nézetei (Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [Rendszernézetek (Transact-SQL)](/sql/t-sql/language-reference/)
