---
title: Az Azure monitorban hasznos operátorok lekérdezések naplózását |} A Microsoft Docs
description: Az Azure monitorban különböző helyzetekben használandó általános funkciókat lekérdezések naplózását.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: bwren
ms.openlocfilehash: d11445c3f31f9aced6fdb9783575d10a026de1f0
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56000148"
---
# <a name="useful-operators-in-azure-monitor-log-queries"></a>Az Azure monitorban hasznos operátorok lekérdezések naplózását.

Az alábbi táblázat a különböző helyzetekhez, az Azure Monitor log-lekérdezések használata néhány gyakori funkciókat biztosít.

## <a name="useful-operators"></a>Hasznos operátorok

Kategória                                |Megfelelő analitikai függvény
----------------------------------------|----------------------------------------
Kijelölés és oszlop aliasok            |`project`, `project-away`, `extend`
Az ideiglenes táblák és állandók          |`let scalar_alias_name = …;` <br> `let table_alias_name =  …  …  … ;`| 
Összehasonlítás és karakterlánc-operátorok         |`startswith`, `!startswith`, `has`, `!has` <br> `contains`, `!contains`, `containscs` <br> `hasprefix`, `!hasprefix`, `hassuffix`, `!hassuffix`, `in`, `!in` <br> `matches regex` <br> `==`, `=~`, `!=`, `!~`
Karakterlánc-függvények                 |`strcat()`, `replace()`, `tolower()`, `toupper()`, `substring()`, `strlen()`
Közös matematikai függvények                   |`sqrt()`, `abs()` <br> `exp()`, `exp2()`, `exp10()`, `log()`, `log2()`, `log10()`, `pow()` <br> `gamma()`, `gammaln()`
Szöveg elemzése                            |`extract()`, `extractjson()`, `parse`, `split()`
Kimeneti korlátozása                         |`take`, `limit`, `top`, `sample`
Dátumfüggvények                          |`now()`, `ago()` <br> `datetime()`, `datepart()`, `timespan` <br> `startofday()`, `startofweek()`, `startofmonth()`, `startofyear()` <br> `endofday()`, `endofweek()`, `endofmonth()`, `endofyear()` <br> `dayofweek()`, `dayofmonth()`, `dayofyear()` <br> `getmonth()`, `getyear()`, `weekofyear()`, `monthofyear()`
Csoportosítás és összesítés                |`summarize by` <br> `max()`, `min()`, `count()`, `dcount()`, `avg()`, `sum()` <br> `stddev()`, `countif()`, `dcountif()`, `argmax()`, `argmin()` <br> `percentiles()`, `percentile_array()`
Illesztés és illesztésre                        |`join kind=leftouter`, `inner`, `rightouter`, `fullouter`, `leftanti` <br> `union`
Rendezési sorrend                             |`sort`, `order` 
Dinamikus objektum (JSON és array)         |`parsejson()` <br> `makeset()`, `makelist()` <br> `split()`, `arraylength()` <br> `zip()`, `pack()`
Logikai operátorok                       |`and`, `or`, `iff(condition, value_t, value_f)` <br> `binary_and()`, `binary_or()`, `binary_not()`, `binary_xor()`
Gépi tanulás                        |`evaluate autocluster`, `basket`, `diffpatterns`, `extractcolumns`


## <a name="next-steps"></a>További lépések

- A lecke meg a [log lekérdezések írásának módját az Azure monitorban](get-started-queries.md).
