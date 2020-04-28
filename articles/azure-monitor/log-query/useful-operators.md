---
title: Hasznos operátorok a Azure Monitor log lekérdezésekben | Microsoft Docs
description: Az Azure Monitor-naplózási lekérdezésekben különböző forgatókönyvekhez használt általános függvények.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: ff63b9b7027e99c70971230936ed98186c2208e8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75397712"
---
# <a name="useful-operators-in-azure-monitor-log-queries"></a>Hasznos operátorok Azure Monitor log-lekérdezésekben

Az alábbi táblázat néhány gyakori függvényt biztosít a különböző forgatókönyvekhez Azure Monitor naplózási lekérdezésekben.

## <a name="useful-operators"></a>Hasznos operátorok

Kategória                                |Kapcsolódó elemzési függvény
----------------------------------------|----------------------------------------
Kijelölési és oszlopbeli aliasok            |`project`, `project-away`, `extend`
Ideiglenes táblák és állandók          |`let scalar_alias_name = …;` <br> `let table_alias_name =  …  …  … ;`| 
Összehasonlítás és karakterlánc-operátorok         |`startswith`, `!startswith`, `has`, `!has` <br> `contains`, `!contains`, `containscs` <br> `hasprefix`, `!hasprefix`, `hassuffix`, `!hassuffix`, `in`, `!in` <br> `matches regex` <br> `==`, `=~`, `!=`, `!~`
Gyakori karakterlánc-függvények                 |`strcat()`, `replace()`, `tolower()`, `toupper()`, `substring()`, `strlen()`
Gyakori matematikai függvények                   |`sqrt()`, `abs()` <br> `exp()`, `exp2()`, `exp10()`, `log()`, `log2()`, `log10()`, `pow()` <br> `gamma()`, `gammaln()`
Szöveg elemzése                            |`extract()`, `extractjson()`, `parse`, `split()`
Kimenet korlátozása                         |`take`, `limit`, `top`, `sample`
Dátumfüggvények                          |`now()`, `ago()` <br> `datetime()`, `datepart()`, `timespan` <br> `startofday()`, `startofweek()`, `startofmonth()`, `startofyear()` <br> `endofday()`, `endofweek()`, `endofmonth()`, `endofyear()` <br> `dayofweek()`, `dayofmonth()`, `dayofyear()` <br> `getmonth()`, `getyear()`, `weekofyear()`, `monthofyear()`
Csoportosítás és összesítés                |`summarize by` <br> `max()`, `min()`, `count()`, `dcount()`, `avg()`, `sum()` <br> `stddev()`, `countif()`, `dcountif()`, `argmax()`, `argmin()` <br> `percentiles()`, `percentile_array()`
Csatlakozások és szakszervezetek                        |`join kind=leftouter`, `inner`, `rightouter`, `fullouter`, `leftanti` <br> `union`
Rendezés, sorrend                             |`sort`, `order` 
Dinamikus objektum (JSON és Array)         |`parsejson()` <br> `makeset()`, `makelist()` <br> `split()`, `arraylength()` <br> `zip()`, `pack()`
Logikai operátorok                       |`and`, `or`, `iff(condition, value_t, value_f)` <br> `binary_and()`, `binary_or()`, `binary_not()`, `binary_xor()`
Gépi tanulás                        |`evaluate autocluster`, `basket`, `diffpatterns`, `extractcolumns`


## <a name="next-steps"></a>További lépések

- Ugorjon végig egy leckét a [Azure monitor írási napló lekérdezéséhez](get-started-queries.md).
