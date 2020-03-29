---
title: Hasznos operátorok az Azure Monitor naplólekérdezésében | Microsoft dokumentumok
description: Az Azure Monitor naplólekérdezéseiben a különböző forgatókönyvekhez használható gyakori függvények.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: ff63b9b7027e99c70971230936ed98186c2208e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75397712"
---
# <a name="useful-operators-in-azure-monitor-log-queries"></a>Hasznos operátorok az Azure Monitor naplólekérdezéseiben

Az alábbi táblázat néhány gyakori függvényt tartalmaz az Azure Monitor naplólekérdezéseinek különböző forgatókönyveihez.

## <a name="useful-operators"></a>Hasznos operátorok

Kategória                                |Releváns elemzési funkció
----------------------------------------|----------------------------------------
Kijelölés és oszlopaliasok            |`project`, `project-away`, `extend`
Ideiglenes táblák és állandók          |`let scalar_alias_name = …;` <br> `let table_alias_name =  …  …  … ;`| 
Összehasonlítás és karakterlánc-operátorok         |`startswith`, `!startswith`, `has`, `!has` <br> `contains`, `!contains`, `containscs` <br> `hasprefix`, `!hasprefix`, `hassuffix`, `!hassuffix`, `in`, `!in` <br> `matches regex` <br> `==`, `=~`, `!=`, `!~`
Gyakori karakterlánc-függvények                 |`strcat()`, `replace()`, `tolower()`, `toupper()`, `substring()`, `strlen()`
Gyakori matematikai függvények                   |`sqrt()`, `abs()` <br> `exp()`, `exp2()`, `exp10()`, `log()`, `log2()`, `log10()`, `pow()` <br> `gamma()`, `gammaln()`
Szöveg elemzése                            |`extract()`, `extractjson()`, `parse`, `split()`
A kimenet korlátozása                         |`take`, `limit`, `top`, `sample`
Dátumfüggvények                          |`now()`, `ago()` <br> `datetime()`, `datepart()`, `timespan` <br> `startofday()`, `startofweek()`, `startofmonth()`, `startofyear()` <br> `endofday()`, `endofweek()`, `endofmonth()`, `endofyear()` <br> `dayofweek()`, `dayofmonth()`, `dayofyear()` <br> `getmonth()`, `getyear()`, `weekofyear()`, `monthofyear()`
Csoportosítás és összesítés                |`summarize by` <br> `max()`, `min()`, `count()`, `dcount()`, `avg()`, `sum()` <br> `stddev()`, `countif()`, `dcountif()`, `argmax()`, `argmin()` <br> `percentiles()`, `percentile_array()`
Csatlakozások és szakszervezetek                        |`join kind=leftouter`, `inner`, `rightouter`, `fullouter`, `leftanti` <br> `union`
Rendezés, sorrend                             |`sort`, `order` 
Dinamikus objektum (JSON és tömb)         |`parsejson()` <br> `makeset()`, `makelist()` <br> `split()`, `arraylength()` <br> `zip()`, `pack()`
Logikai operátorok                       |`and`, `or`, `iff(condition, value_t, value_f)` <br> `binary_and()`, `binary_or()`, `binary_not()`, `binary_xor()`
Gépi tanulás                        |`evaluate autocluster`, `basket`, `diffpatterns`, `extractcolumns`


## <a name="next-steps"></a>További lépések

- Az Azure Monitor [írási naplólekérdezéseiről](get-started-queries.md)szóló leckében halad át.
