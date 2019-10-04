---
title: Rendszerfüggvények Azure Cosmos DB lekérdezési nyelven
description: A Azure Cosmos DB SQL-rendszerfunkcióinak megismerése.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 98a087f02035545b828cfcf11c58d03d02a5e76a
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349172"
---
# <a name="system-functions-azure-cosmos-db"></a>Rendszerfunkciók (Azure Cosmos DB)

 A cosmos DB számos beépített SQL-funkciókat biztosít. A beépített függvénykategóriákat alább láthatók.  
  
|Csoport|Leírás|Műveletek|  
|--------------|-----------------|-----------------| 
|[Tömb funkciók](sql-query-array-functions.md)|A tömb függvények végrehajtania egy műveletet a egy tömb bemeneti érték és a visszaadott numerikus, a logikai vagy a tömb értéket. | [ARRAY_CONCAT](sql-query-array-concat.md), [ARRAY_CONTAINS](sql-query-array-contains.md), [ARRAY_LENGTH](sql-query-array-length.md), [ARRAY_SLICE](sql-query-array-slice.md) |
|[Dátum és idő függvények](sql-query-date-time-functions.md)|A dátum-és időfüggvények lehetővé teszik az aktuális UTC dátum és idő megszerzését két formában; egy numerikus időbélyeg, amelynek értéke a UNIX-kor ezredmásodpercben, vagy egy olyan karakterlánc, amely megfelel az ISO 8601 formátumnak. | [GetCurrentDateTime](sql-query-getcurrentdatetime.md), [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md) |
|[Matematikai függvények](sql-query-mathematical-functions.md)|A matematika függvényekkel végezhet a számítást, általában argumentumként szolgálnak, és a egy numerikus értéket adja vissza a bemeneti értékek alapján. | [ABS](sql-query-abs.md), [ACO](sql-query-acos.md)- [ASIN](sql-query-asin.md), [ATAN](sql-query-atan.md), [ATN2](sql-query-atn2.md), [MENNYEZET](sql-query-ceiling.md), [COS](sql-query-cos.md), [GYERMEKÁGY](sql-query-cot.md), [FOK](sql-query-degrees.md), [EXP](sql-query-exp.md), [FLOOR](sql-query-floor.md), [LOG](sql-query-log.md), [LOG10](sql-query-log10.md), [PI](sql-query-pi.md), [POWER](sql-query-power.md), [RADIÁN](sql-query-radians.md), [RAND](sql-query-rand.md), [ROUND](sql-query-round.md), [SIGN](sql-query-sign.md), [SIN](sql-query-sin.md), [SQRT](sql-query-sqrt.md), [SQUARE](sql-query-square.md), [TAN](sql-query-tan.md), [TRUNC](sql-query-trunc.md) |
|[Térbeli funkciók](sql-query-spatial-functions.md)|A térbeli függvények végrehajtania egy műveletet a térbeli objektum bemeneti érték a, és a egy numerikus vagy logikai értéket adja vissza. | [ST_DISTANCE](sql-query-st-distance.md), [ST_INTERSECTS](sql-query-st-intersects.md), [ST_ISVALID](sql-query-st-isvalid.md), [ST_ISVALIDDETAILED](sql-query-st-isvaliddetailed.md), [ST_WITHIN](sql-query-st-within.md) |
|[Sztringfüggvények](sql-query-string-functions.md)|A karakterlánc-függvények végrehajtania egy műveletet a bemeneti karakterlánc-érték, és a egy karakterlánc, numerikus vagy logikai értéket adja vissza. | [concat](sql-query-concat.md), [tartalmaz](sql-query-contains.md), [ENDSWITH](sql-query-endswith.md), [INDEX_OF](sql-query-index-of.md), [Left](sql-query-left.md), [Length](sql-query-length.md), [Lower](sql-query-lower.md), [LTrim](sql-query-ltrim.md), [replace](sql-query-replace.md), [replikálás](sql-query-replicate.md), [fordított](sql-query-reverse.md), [Right](sql-query-right.md), [RTRIM](sql-query-rtrim.md), [ STARTSWITH](sql-query-startswith.md), [StringToArray](sql-query-stringtoarray.md), [StringToBoolean](sql-query-stringtoboolean.md), [StringToNull](sql-query-stringtonull.md), [StringToNumber](sql-query-stringtonumber.md), [StringToObject](sql-query-stringtoobject.md), [alkarakterlánc](sql-query-substring.md), [ToString](sql-query-tostring.md), [Trim](sql-query-trim.md), [Upper](sql-query-upper.md) |
|[Funkciók ellenőrzése típusa](sql-query-type-checking-functions.md)|A típus ellenőrzése funkciók lehetővé teszik az SQL-lekérdezések belül egy kifejezés típusának ellenőrzése. | [IS_ARRAY](sql-query-is-array.md), [IS_BOOL](sql-query-is-bool.md), [IS_DEFINED](sql-query-is-defined.md), [IS_NULL](sql-query-is-null.md), [IS_NUMBER](sql-query-is-number.md), [IS_OBJECT](sql-query-is-object.md), [IS_PRIMITIVE](sql-query-is-primitive.md), [IS_STRING](sql-query-is-string.md) |

## <a name="built-in-versus-user-defined-functions-udfs"></a>Beépített és felhasználó által definiált függvények (UDF)

Ha jelenleg olyan felhasználó által definiált függvényt (UDF) használ, amelyhez már elérhető egy beépített függvény, a megfelelő beépített függvény gyorsabban fog futni és hatékonyabbá válik.

## <a name="built-in-versus-ansi-sql-functions"></a>Beépített és ANSI SQL-függvények

Cosmos DB függvények és az ANSI SQL functions közötti fő különbség az, hogy a Cosmos DB Functions úgy lett kialakítva, hogy megfelelően működjön a séma nélküli és a vegyes séma adataival. Ha például egy tulajdonság hiányzik vagy nem numerikus értékkel (például `unknown`) rendelkezik, az elem kimarad a hiba helyett.

## <a name="next-steps"></a>További lépések

- [Bevezetés a Azure Cosmos DBba](introduction.md)
- [Tömb funkciók](sql-query-array-functions.md)
- [Dátum és idő függvények](sql-query-date-time-functions.md)
- [Matematikai függvények](sql-query-mathematical-functions.md)
- [Térbeli funkciók](sql-query-spatial-functions.md)
- [Sztringfüggvények](sql-query-string-functions.md)
- [Funkciók ellenőrzése típusa](sql-query-type-checking-functions.md)
- [Felhasználó által definiált függvények](sql-query-udfs.md)
- [Aggregátumok](sql-query-aggregates.md)
