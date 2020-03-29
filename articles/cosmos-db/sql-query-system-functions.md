---
title: Rendszerfüggvények az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg a beépített és a felhasználó által definiált SQL-rendszer függvények az Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6f41adbb726313ef095084d079dc7852736e0c06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74870530"
---
# <a name="system-functions-azure-cosmos-db"></a>Rendszerfüggvények (Azure Cosmos DB)

 A Cosmos DB számos beépített SQL-függvényt biztosít. A beépített függvények kategóriái az alábbiakban találhatók.  
  
|Csoportcsoport|Leírás|Műveletek|  
|--------------|-----------------|-----------------| 
|[Tömb függvények](sql-query-array-functions.md)|A tömbfüggvények egy tömb bemeneti értékén hajtanak végre műveletet, és numerikus, logikai vagy tömbértéket adnak vissza. | [ARRAY_CONCAT,](sql-query-array-concat.md) [ARRAY_CONTAINS,](sql-query-array-contains.md) [ARRAY_LENGTH](sql-query-array-length.md) [ARRAY_SLICE](sql-query-array-slice.md) |
|[Dátum- és időfüggvények](sql-query-date-time-functions.md)|A dátum- és időfüggvények lehetővé teszik, hogy az aktuális UTC-dátumot és időt két formában kapja meg; egy numerikus időbélyeg, amelynek értéke Unix-korszak ezredmásodpercben vagy az ISO 8601 formátumnak megfelelő karakterláncként. | [GetCurrentDateTime](sql-query-getcurrentdatetime.md), [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md) |
|[Matematikai függvények](sql-query-mathematical-functions.md)|A matematikai függvények mindegyike egy-egy számítást hajt végre, általában argumentumként megadott bemeneti értékek alapján, és numerikus értéket ad vissza. | [ABS,](sql-query-abs.md) [ACOS,](sql-query-acos.md) [ASIN,](sql-query-asin.md) [ATAN,](sql-query-atan.md) [ATN2,](sql-query-atn2.md) [MENNYEZET,](sql-query-ceiling.md) [COS,](sql-query-cos.md) [COT,](sql-query-cot.md) [Degrees,](sql-query-degrees.md) [EXP,](sql-query-exp.md) [Floor,](sql-query-floor.md) [LOG,](sql-query-log.md) [LOG10,](sql-query-log10.md) [PI,](sql-query-pi.md) [POWER,](sql-query-power.md) [RADIÁN,](sql-query-radians.md) [RAND,](sql-query-rand.md) [kerek,](sql-query-round.md) [jel,](sql-query-sign.md) [Sin](sql-query-sin.md), [SQRT,](sql-query-sqrt.md) [Négyzet,](sql-query-square.md) [TAN](sql-query-tan.md), [TRUNC](sql-query-trunc.md) |
|[Térbeli függvények](sql-query-spatial-functions.md)|A térbeli függvények egy térbeli objektum bemeneti értékén hajtanak végre műveletet, és numerikus vagy logikai értéket adnak vissza. | [ST_DISTANCE,](sql-query-st-distance.md) [ST_INTERSECTS,](sql-query-st-intersects.md) [ST_ISVALID, ST_ISVALIDDETAILED](sql-query-st-isvaliddetailed.md) [ST_WITHIN](sql-query-st-within.md) [ST_ISVALID](sql-query-st-isvalid.md) |
|[Sztringfüggvények](sql-query-string-functions.md)|A karakterlánc-függvények műveletet hajtanak végre egy karakterlánc bemeneti értékén, és karakterláncot, numerikus vagy logikai értéket adnak vissza. | [CONCAT](sql-query-concat.md), [Tartalmazza](sql-query-contains.md), [endswith,](sql-query-endswith.md) [INDEX_OF, balra,](sql-query-index-of.md) [hossz,](sql-query-length.md) [ALSÓ,](sql-query-lower.md) [LEFT](sql-query-left.md) [LTRIM](sql-query-ltrim.md), [CSERE,](sql-query-replace.md)ISMÉTLÉS, [SZTORNÍR,](sql-query-reverse.md) [Jobbra,](sql-query-right.md) [RTRIM](sql-query-rtrim.md), [STARTSWITH](sql-query-startswith.md), [StringToArray](sql-query-stringtoarray.md), [StringToBoolean](sql-query-stringtoboolean.md), [StringToNull](sql-query-stringtonull.md), [StringToNumber](sql-query-stringtonumber.md), [StringToObject](sql-query-stringtoobject.md), [SUBSTRING](sql-query-substring.md), [ToString](sql-query-tostring.md), [TRIM](sql-query-trim.md), [REPLICATE](sql-query-replicate.md) [UPPER](sql-query-upper.md) |
|[Típusellenőrzési függvények](sql-query-type-checking-functions.md)|A típusellenőrzési függvények lehetővé teszik egy kifejezés típusának ellenőrzését az SQL-lekérdezésekben. | [IS_ARRAY,](sql-query-is-array.md) [IS_BOOL,](sql-query-is-bool.md) [IS_DEFINED,](sql-query-is-defined.md) [IS_NULL,](sql-query-is-null.md) [IS_NUMBER IS_OBJECT,](sql-query-is-number.md)IS_PRIMITIVE [IS_OBJECT](sql-query-is-object.md) [IS_STRING](sql-query-is-primitive.md) [IS_STRING](sql-query-is-string.md) |

## <a name="built-in-versus-user-defined-functions-udfs"></a>Beépített és felhasználó által definiált függvények (UDF-ek)

Ha jelenleg egy felhasználó által definiált függvényt (UDF) használ, amelyhez már elérhető egy beépített függvény, a megfelelő beépített függvény gyorsabban és hatékonyabban fog futni.

## <a name="built-in-versus-ansi-sql-functions"></a>Beépített és ANSI SQL függvények

A fő különbség a Cosmos DB függvények és az ANSI SQL-függvények között az, hogy a Cosmos DB függvények úgy vannak kialakítva, hogy jól működjenek a séma nélküli és vegyes sémaadatokkal. Ha például egy tulajdonság hiányzik, vagy nem `unknown`numerikus értéke hasonló, a program a hibát nem ad vissza.

## <a name="next-steps"></a>További lépések

- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
- [Tömb függvények](sql-query-array-functions.md)
- [Dátum és időpont függvényei](sql-query-date-time-functions.md)
- [Matematikai függvények](sql-query-mathematical-functions.md)
- [Térbeli függvények](sql-query-spatial-functions.md)
- [Sztringfüggvények](sql-query-string-functions.md)
- [Típusellenőrzési függvények](sql-query-type-checking-functions.md)
- [Felhasználó által definiált függvények](sql-query-udfs.md)
- [Összesítések](sql-query-aggregates.md)
