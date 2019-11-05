---
title: Huzavona adatfolyam-átalakítási függvények a Azure Data Factoryban | Microsoft Docs
description: A Azure Data Factory elérhető huzavona-adatáramlási funkcióinak áttekintése
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 73ad0b39e119a9ef33fa1fa595ddcc47f61ae163
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518553"
---
# <a name="transformation-functions-in-wrangling-data-flow"></a>Transzformációs függvények a huzavona-adatfolyamban

A Azure Data Factory huzavona-adatfolyama lehetővé teszi a kód nélküli agilis adatelőkészítést és huzavona a Felhőbeli méretezéssel. A huzavona-adatfolyam integrálható [Power Query online](https://docs.microsoft.com/powerquery-m/power-query-m-reference) -nal, és a Spark-végrehajtással elérhetővé teszi a Power Query M funkciókat az adatok huzavona. 

Jelenleg nem minden Power Query M függvény támogatott az adatok huzavona, annak ellenére, hogy a létrehozás során elérhetővé válik. A huzavona-adatfolyamatok létrehozásakor a rendszer a következő hibaüzenetet fogja kérni, ha egy függvény nem támogatott:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Az alábbi lista a támogatott Power Query M függvények listáját tartalmazza.

## <a name="column-management"></a>Oszlopok kezelése

* Kiválasztás: [table. SelectColumns](https://docs.microsoft.com/powerquery-m/table-selectcolumns)
* Eltávolítás: [table. RemoveColumns](https://docs.microsoft.com/powerquery-m/table-removecolumns)
* Átnevezés: [table. RenameColumns](https://docs.microsoft.com/powerquery-m/table-renamecolumns), [table. PrefixColumns](https://docs.microsoft.com/powerquery-m/table-prefixcolumns), [table. TransformColumnNames](https://docs.microsoft.com/powerquery-m/table-transformcolumnnames)
* Átrendezés: [table. ReorderColumns](https://docs.microsoft.com/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>Sorok szűrése

Az M Function [table. SelectRows](https://docs.microsoft.com/powerquery-m/table-selectrows) használatával szűrheti a következő feltételeket:

* Egyenlőség és egyenlőtlenség
* Numerikus, szöveges és dátum típusú összehasonlítások (de nem DateTime)
* Numerikus adatok, például [Number. IsEven](https://docs.microsoft.com/powerquery-m/number-iseven)/[páratlan](https://docs.microsoft.com/powerquery-m/number-iseven)
* Szöveg [szöveggel](https://docs.microsoft.com/powerquery-m/text-contains)történő elszigetelése. tartalmaz, [Text. StartsWith](https://docs.microsoft.com/powerquery-m/text-startswith)vagy [Text. EndsWith](https://docs.microsoft.com/powerquery-m/text-endswith)
* Dátumtartomány, beleértve az összes "IsIn' [Date functions" függvényt](https://docs.microsoft.com/powerquery-m/date-functions) 
* Ezek kombinációja a és a, a vagy a feltételekkel

## <a name="adding-and-transforming-columns"></a>Oszlopok hozzáadása és átalakítása

A következő M függvény oszlopok hozzáadása vagy átalakítása: [table. AddColumn](https://docs.microsoft.com/powerquery-m/table-addcolumn), [table. TransformColumns](https://docs.microsoft.com/powerquery-m/table-transformcolumns), [table. ReplaceValue](https://docs.microsoft.com/powerquery-m/table-replacevalue), [table. DuplicateColumn](https://docs.microsoft.com/powerquery-m/table-duplicatecolumn). Alább láthatók a támogatott transzformációs függvények.

* Numerikus aritmetika
* Szöveg összefűzése
* Dátum andTime aritmetika (aritmetikai operátorok, [Date. AddDays](https://docs.microsoft.com/powerquery-m/date-adddays), [Date. AddMonths](https://docs.microsoft.com/powerquery-m/date-addmonths), [Date. AddQuarters](https://docs.microsoft.com/powerquery-m/date-addquarters), [Date. AddWeeks](https://docs.microsoft.com/powerquery-m/date-addweeks), [Date. AddYears](https://docs.microsoft.com/powerquery-m/date-addyears))
* Az időtartamok használhatók dátum és idő aritmetikaára, de a fogadóba való írás előtt egy másik típusba kell alakítani (aritmetikai operátorok, [#duration](https://docs.microsoft.com/powerquery-m/sharpduration), [időtartam. nap](https://docs.microsoft.com/powerquery-m/duration-days), [időtartam. óra](https://docs.microsoft.com/powerquery-m/duration-hours), [időtartam. perc](https://docs.microsoft.com/powerquery-m/duration-minutes), [ Időtartam. másodperc](https://docs.microsoft.com/powerquery-m/duration-seconds), [időtartam. TotalDays](https://docs.microsoft.com/powerquery-m/duration-totaldays), [időtartam. TotalHours](https://docs.microsoft.com/powerquery-m/duration-totalhours), [időtartam. összesperc](https://docs.microsoft.com/powerquery-m/duration-totalminutes), [időtartam. összesmásodperc](https://docs.microsoft.com/powerquery-m/duration-totalseconds))    
* A standard, a tudományos és a trigonometriai numerikus függvények (az összes [művelet](https://docs.microsoft.com/powerquery-m/number-functions#operations), [kerekítés](https://docs.microsoft.com/powerquery-m/number-functions#rounding)és [trigonometria](https://docs.microsoft.com/powerquery-m/number-functions#trigonometry) , *kivéve* a Number. faktoriálisát, Number. permutációs és Number. kombinációkat)
* Pótlás ([áthelyező. ReplaceText](https://docs.microsoft.com/powerquery-m/replacer-replacetext), [áthelyező. ReplaceValue](https://docs.microsoft.com/powerquery-m/replacer-replacevalue), [szöveg. csere](https://docs.microsoft.com/powerquery-m/text-replace), [szöveg. Eltávolítás](https://docs.microsoft.com/powerquery-m/text-remove))
* Elhelyezési szöveg kibontása ([Text. PositionOf](https://docs.microsoft.com/powerquery-m/text-positionof), [Text. length](https://docs.microsoft.com/powerquery-m/text-length), [Text. Start](https://docs.microsoft.com/powerquery-m/text-start), [Text. End](https://docs.microsoft.com/powerquery-m/text-end), [Text. Middle](https://docs.microsoft.com/powerquery-m/text-middle), [Text. ReplaceRange](https://docs.microsoft.com/powerquery-m/text-replacerange), [Text. RemoveRange](https://docs.microsoft.com/powerquery-m/text-removerange))
* Alapszintű szövegformázás[(Text. Lower](https://docs.microsoft.com/powerquery-m/text-lower), [Text. Upper](https://docs.microsoft.com/powerquery-m/text-upper), [text. Trim](https://docs.microsoft.com/powerquery-m/text-trim)/[kezdő](https://docs.microsoft.com/powerquery-m/text-trimstart)/[End](https://docs.microsoft.com/powerquery-m/text-trimend), [Text. PadStart](https://docs.microsoft.com/powerquery-m/text-padstart)/[End](https://docs.microsoft.com/powerquery-m/text-padend), [Text. reverse](https://docs.microsoft.com/powerquery-m/text-reverse))
* Dátum-és időfüggvények (dátum[. nap](https://docs.microsoft.com/powerquery-m/date-day), [dátum. hónap](https://docs.microsoft.com/powerquery-m/date-month), [dátum. év](https://docs.microsoft.com/powerquery-m/date-year) [idő. óra](https://docs.microsoft.com/powerquery-m/time-hour), [idő. perc](https://docs.microsoft.com/powerquery-m/time-minute), [idő. másodperc](https://docs.microsoft.com/powerquery-m/time-second), [dátum. DayOfWeek](https://docs.microsoft.com/powerquery-m/date-dayofweek), [Date. DayOfYear](https://docs.microsoft.com/powerquery-m/date-dayofyear), [Date. DaysInMonth](https://docs.microsoft.com/powerquery-m/date-daysinmonth))
* Ha a kifejezések (de az ágaknak egyező típusúnak kell lenniük)
* Sorok szűrése logikai oszlopként
* Szám, szöveg, logikai, dátum és DateTime állandó

<a name="mergingjoining-tables"></a>Egyesítés/csatlakozás táblákhoz
----------------------
* Power Query egy beágyazott illesztést fog létrehozni (Table. NestedJoin; a felhasználók manuálisan is írhatnak [table. AddJoinColumn](https://docs.microsoft.com/powerquery-m/table-addjoincolumn)).
    A felhasználóknak ezt követően ki kell bontaniuk a beágyazott illesztési oszlopot egy nem beágyazott illesztésbe (Table. ExpandTableColumn, más kontextusban nem támogatott).
* Az M Function [tábla. az JOIN](https://docs.microsoft.com/powerquery-m/table-join) közvetlenül is írható, így elkerülhető, hogy további bővítési lépésre van szükség, de a felhasználónak biztosítania kell, hogy ne legyenek duplikált oszlopnevek az összekapcsolt táblák között.
* Támogatott illesztési típus: [Inner](https://docs.microsoft.com/powerquery-m/joinkind-inner), [LeftOuter](https://docs.microsoft.com/powerquery-m/joinkind-leftouter), [RightOuter](https://docs.microsoft.com/powerquery-m/joinkind-rightouter), [FullOuter](https://docs.microsoft.com/powerquery-m/joinkind-fullouter)
* A [Value. Equals](https://docs.microsoft.com/powerquery-m/value-equals) és az [Value. NullableEquals](https://docs.microsoft.com/powerquery-m/value-nullableequals) egyaránt támogatott a kulcsfontosságú esélyegyenlőségi Összehasonlításként

## <a name="group-by"></a>Csoportosítási alapja

A [table. Group](https://docs.microsoft.com/powerquery-m/table-group) használatával összesítheti az értékeket.
* Összesítési függvénnyel kell használni
* Támogatott aggregációs függvények: [table. sorszám](https://docs.microsoft.com/powerquery-m/table-rowcount), [List. SZUM](https://docs.microsoft.com/powerquery-m/list-sum), [lista. Count](https://docs.microsoft.com/powerquery-m/list-count), [Listázás. Average](https://docs.microsoft.com/powerquery-m/list-average), [lista. min](https://docs.microsoft.com/powerquery-m/list-min), [lista. max](https://docs.microsoft.com/powerquery-m/list-max), [List. StandardDeviation](https://docs.microsoft.com/powerquery-m/list-standarddeviation), [Listázás. First](https://docs.microsoft.com/powerquery-m/list-first), [Listázás. Last](https://docs.microsoft.com/powerquery-m/list-last)

## <a name="sorting"></a>Rendezés

Az értékek rendezéséhez használja a [table. sort](https://docs.microsoft.com/powerquery-m/table-sort) .

## <a name="reducing-rows"></a>Sorok csökkentése

Tartsa meg és távolítsa el a Top, Keep Range (megfelelő M függvények, csak a támogatási számok, nem feltételek: [table. FirstN](https://docs.microsoft.com/powerquery-m/table-firstn), [table. skip](https://docs.microsoft.com/powerquery-m/table-skip), [table. RemoveFirstN](https://docs.microsoft.com/powerquery-m/table-removefirstn), [table. Range](https://docs.microsoft.com/powerquery-m/table-range), table. [MinN](https://docs.microsoft.com/powerquery-m/table-minn), [table. MaxN](https://docs.microsoft.com/powerquery-m/table-maxn))

## <a name="known-unsupported-functionality"></a>Ismert nem támogatott funkciók

Az alábbi függvények nem támogatottak. Ez a lista nem teljes, és változhat.
* Oszlopok egyesítése (a AddColumn használatával érhető el)
* Oszlop felosztása
* Lekérdezések hozzáfűzése
* "Az első sor használata fejlécként" és "fejlécek használata első sorként"

## <a name="next-steps"></a>További lépések
