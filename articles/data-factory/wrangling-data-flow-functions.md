---
title: Az Azure Data Factory ban az adatfolyam-átalakítási funkciók
description: Az Azure Data Factory ban elérhető vonódási adatfolyam-függvények áttekintése
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: e2517ec4a02a5d61fb3ce1d9ca9ffa2b5f4e8bf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74287023"
---
# <a name="transformation-functions-in-wrangling-data-flow"></a>Átalakítási függvények az adatfolyam kibogozásában

Az Azure Data Factory ban az adatfolyam okozása lehetővé teszi, hogy kódmentes agilis adatelőkészítést és felhőszintű huzavonát végezze. A vonódsás adatfolyam integrálható a [Power Query Online-nal,](https://docs.microsoft.com/powerquery-m/power-query-m-reference) és elérhetővé teszi a Power Query M funkciókat a szikravégrehajtáson keresztüli adatvonókodáshoz. 

Jelenleg nem minden Power Query M függvény támogatott az adatveréshez, annak ellenére, hogy a szerzői jog során elérhető. A huzavona adatfolyamainak létrehozása során a következő hibaüzenet jelenik meg, ha egy függvény nem támogatott:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Az alábbi lista a támogatott Power Query M függvényeket tartalmazza.

## <a name="column-management"></a>Oszlopkezelés

* Kijelölés: [Table.SelectColumns](https://docs.microsoft.com/powerquery-m/table-selectcolumns)
* Eltávolítás: [Table.RemoveColumns](https://docs.microsoft.com/powerquery-m/table-removecolumns)
* Átnevezés: [Table.RenameColumns](https://docs.microsoft.com/powerquery-m/table-renamecolumns), [Table.PrefixColumns](https://docs.microsoft.com/powerquery-m/table-prefixcolumns), [Table.TransformColumnNames](https://docs.microsoft.com/powerquery-m/table-transformcolumnnames)
* Átrendezés: [Table.ReorderColumns](https://docs.microsoft.com/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>Sorszűrés

A [Table.SelectRows](https://docs.microsoft.com/powerquery-m/table-selectrows) függvény t használja a következő feltételekre történő szűrést:

* Egyenlőség és egyenlőtlenség
* Numerikus, szöveges és dátum-összehasonlítások (de datetime nem)
* Numerikus adatok, például [Number.IsEven](https://docs.microsoft.com/powerquery-m/number-iseven)/[Páratlan](https://docs.microsoft.com/powerquery-m/number-iseven)
* Szövegelszigetelés [szöveghasználatával.Tartalmazza](https://docs.microsoft.com/powerquery-m/text-contains), [Szöveg.StartsWith](https://docs.microsoft.com/powerquery-m/text-startswith)vagy [Text.EndsWith](https://docs.microsoft.com/powerquery-m/text-endswith)
* Dátumtartományok, beleértve az összes "IsIn" [dátum függvényt)](https://docs.microsoft.com/powerquery-m/date-functions) 
* Ezek kombinációi a használat és, vagy nem feltételek

## <a name="adding-and-transforming-columns"></a>Oszlopok hozzáadása és átalakítása

A következő M függvények adnak hozzá vagy alakítanak át oszlopokat: [Table.AddColumn](https://docs.microsoft.com/powerquery-m/table-addcolumn), [Table.TransformColumns](https://docs.microsoft.com/powerquery-m/table-transformcolumns), [Table.ReplaceValue](https://docs.microsoft.com/powerquery-m/table-replacevalue), [Table.DuplicateColumn](https://docs.microsoft.com/powerquery-m/table-duplicatecolumn). Az alábbiakban a támogatott átalakítási függvények.

* Numerikus aritmetikai
* Szöveg összefűzése
* Dátum és idő számtani (Aritmetikai operátorok, [Date.AddDays](https://docs.microsoft.com/powerquery-m/date-adddays), [Date.AddMonths](https://docs.microsoft.com/powerquery-m/date-addmonths), [Date.AddQuarters](https://docs.microsoft.com/powerquery-m/date-addquarters), [Date.AddWeeks](https://docs.microsoft.com/powerquery-m/date-addweeks), [Date.AddYears , Date.AddYears](https://docs.microsoft.com/powerquery-m/date-addyears))
* Az időtartamok felhasználhatók dátum- és időszámtanként, de a fogadóba írás előtt át kell alakítani őket egy másik típussá (aritmetikai operátorok, [#duration,](https://docs.microsoft.com/powerquery-m/sharpduration) [Időtartam.Napok,](https://docs.microsoft.com/powerquery-m/duration-days) [Időtartam.Órák,](https://docs.microsoft.com/powerquery-m/duration-hours) [Időtartam.Perc,](https://docs.microsoft.com/powerquery-m/duration-minutes) [Időtartam.Másodperc](https://docs.microsoft.com/powerquery-m/duration-seconds), [Időtartam.TotalDays](https://docs.microsoft.com/powerquery-m/duration-totaldays), [Duration.TotalMinutes](https://docs.microsoft.com/powerquery-m/duration-totalhours), [Duration.TotalSeconds](https://docs.microsoft.com/powerquery-m/duration-totalseconds)) [Duration.TotalMinutes](https://docs.microsoft.com/powerquery-m/duration-totalminutes)    
* A legtöbb szabványos, tudományos és trigonometriai numerikus függvény [(a Műveletek,](https://docs.microsoft.com/powerquery-m/number-functions#operations) [Kerekítés](https://docs.microsoft.com/powerquery-m/number-functions#rounding)és [Trigonometria](https://docs.microsoft.com/powerquery-m/number-functions#trigonometry) minden függvénye, *kivéve* a Number.Factorial, Number.Permutációk és Number.Combinations függvényeket)
* Csere ([Replacer.ReplaceText](https://docs.microsoft.com/powerquery-m/replacer-replacetext), [Replacer.ReplaceValue](https://docs.microsoft.com/powerquery-m/replacer-replacevalue), [Text.Replace](https://docs.microsoft.com/powerquery-m/text-replace), [Text.Remove](https://docs.microsoft.com/powerquery-m/text-remove))
* Pozicionált szöveg kihúzása ([Text.PositionOf](https://docs.microsoft.com/powerquery-m/text-positionof), [Text.Length](https://docs.microsoft.com/powerquery-m/text-length), [Text.Start](https://docs.microsoft.com/powerquery-m/text-start), [Text.End](https://docs.microsoft.com/powerquery-m/text-end), [Text.Middle](https://docs.microsoft.com/powerquery-m/text-middle), [Text.ReplaceRange](https://docs.microsoft.com/powerquery-m/text-replacerange), [Text.RemoveRange](https://docs.microsoft.com/powerquery-m/text-removerange))
* Egyszerű szövegformázás ([Szöveg.Alsó,](https://docs.microsoft.com/powerquery-m/text-lower) [Szöveg.Felső](https://docs.microsoft.com/powerquery-m/text-upper), [Szöveg.Kezdet](https://docs.microsoft.com/powerquery-m/text-trim)/[Start](https://docs.microsoft.com/powerquery-m/text-trimstart)/[vége](https://docs.microsoft.com/powerquery-m/text-trimend)vágása , [Szöveg.PadBefejezés](https://docs.microsoft.com/powerquery-m/text-padstart)/[vége](https://docs.microsoft.com/powerquery-m/text-padend), [Szöveg.Fordított](https://docs.microsoft.com/powerquery-m/text-reverse))
* Dátum/idő függvények ([dátum.nap,](https://docs.microsoft.com/powerquery-m/date-day) [dátum.hónap](https://docs.microsoft.com/powerquery-m/date-month), [dátum.idő.óra](https://docs.microsoft.com/powerquery-m/date-year) [Time.Hour](https://docs.microsoft.com/powerquery-m/time-hour), [idő.perc](https://docs.microsoft.com/powerquery-m/time-minute), [idő.másodperc,](https://docs.microsoft.com/powerquery-m/time-second) [dátum.hét,](https://docs.microsoft.com/powerquery-m/date-dayofweek) [dátum.év,](https://docs.microsoft.com/powerquery-m/date-dayofyear) [dátum.napokhónap](https://docs.microsoft.com/powerquery-m/date-daysinmonth))
* Ha a kifejezések (de az ágaknak egyező típusúaknak kell lennie)
* Sorszűrők logikai oszlopként
* Szám-, szöveg-, logikai, dátum- és dátumidő-állandók

<a name="mergingjoining-tables"></a>Táblázatok egyesítése/illesztése
----------------------
* A Power Query beágyazott illesztést hoz létre (Table.NestedJoin; a felhasználók manuálisan is írhatják [a Table.AddJoinColumn oszlopot](https://docs.microsoft.com/powerquery-m/table-addjoincolumn)).
    A felhasználóknak ezután ki kell bontaniuk a beágyazott illesztési oszlopot nem beágyazott illesztésre (Table.ExpandTableColumn, más környezetben nem támogatott).
* Az M függvény [Table.Join](https://docs.microsoft.com/powerquery-m/table-join) lehet írni közvetlenül, hogy ne kelljen egy további kibontási lépés, de a felhasználónak meg kell győződnie arról, hogy nincsenek ismétlődő oszlopnevek között az illesztett táblák
* Támogatott illesztési fajták: [Belső,](https://docs.microsoft.com/powerquery-m/joinkind-inner) [LeftOuter](https://docs.microsoft.com/powerquery-m/joinkind-leftouter), [RightOuter](https://docs.microsoft.com/powerquery-m/joinkind-rightouter), [FullOuter](https://docs.microsoft.com/powerquery-m/joinkind-fullouter)
* Mind [value.Equals](https://docs.microsoft.com/powerquery-m/value-equals) és [Value.NullableEquals](https://docs.microsoft.com/powerquery-m/value-nullableequals) támogatja a legfontosabb egyenlőség összehasonlítása

## <a name="group-by"></a>Csoportosítási szempont

A [Table.Group](https://docs.microsoft.com/powerquery-m/table-group) használatával összesítse az értékeket.
* Összesítési funkcióval kell használni.
* Támogatott összesítési függvények: [Table.RowCount](https://docs.microsoft.com/powerquery-m/table-rowcount), [List.Sum](https://docs.microsoft.com/powerquery-m/list-sum), [List.Count](https://docs.microsoft.com/powerquery-m/list-count), [List.Average](https://docs.microsoft.com/powerquery-m/list-average), [List.Min](https://docs.microsoft.com/powerquery-m/list-min), [List.Max](https://docs.microsoft.com/powerquery-m/list-max), [List.StandardDeviation](https://docs.microsoft.com/powerquery-m/list-standarddeviation), [List.First](https://docs.microsoft.com/powerquery-m/list-first), [List.Last](https://docs.microsoft.com/powerquery-m/list-last)

## <a name="sorting"></a>Rendezés

Értékek rendezése a [Table.Sort](https://docs.microsoft.com/powerquery-m/table-sort) segítségével.

## <a name="reducing-rows"></a>Sorok csökkentése

Felső megtartása és eltávolítása, Tartomány megtartása (megfelelő M függvények, csak a támogató számok, nem feltételek: [Table.FirstN](https://docs.microsoft.com/powerquery-m/table-firstn), [Table.Skip](https://docs.microsoft.com/powerquery-m/table-skip), [Table.RemoveFirstN](https://docs.microsoft.com/powerquery-m/table-removefirstn), [Table.Range](https://docs.microsoft.com/powerquery-m/table-range), [Table.MinN](https://docs.microsoft.com/powerquery-m/table-minn), [Table.MaxN](https://docs.microsoft.com/powerquery-m/table-maxn))

## <a name="known-unsupported-functions"></a>Ismert nem támogatott függvények

| Függvény | status |
| -- | -- |
| Table.PromoteHeaders | Nem támogatott. Ugyanez az eredmény az adatkészlet "Első sor fejlécként" beállításával érhető el. |
| Table.CombineColumns | Ez egy gyakori forgatókönyv, amely közvetlenül nem támogatott, de érhető el egy új oszlop hozzáadásával, amely összefűz két adott oszlopot.  Például Table.AddColumn(RemoveEmailColumn, "Name", mindegyik [Keresztnév] & " & [Vezetéknév]) |
| Table.TransformColumnTypes | Ez a legtöbb esetben támogatott. A következő esetek nem támogatottak: karakterlánc átalakítása pénznemtípussá, karakterlánc átalakítása időtípussá, karakterlánc átalakítása Százalék típusúvá. |
| Table.NestedJoin | Csak csinál egy illesztés akarat eredmény -ban egy érvényesítés hiba. Az oszlopokat ki kell bontani ahhoz, hogy működjenek. |
| Table.Distinct | Az ismétlődő sorok eltávolítása nem támogatott. |
| Table.RemoveLastN | Az alsó sorok eltávolítása nem támogatott. |
| Table.RowCount | Nem támogatott, de egy olyan összeadásoszloptal érhető el, amelyen az összes cella üres (a feltételoszlop használható), majd csoportonként használható az adott oszlopban. Table.Group támogatott. | 
| Sorszintű hibakezelés | A sorszintű hibakezelés jelenleg nem támogatott. Ha például ki szeretné szűrni a nem numerikus értékeket egy oszlopból, az egyik módszer az lenne, ha a szövegoszlopot számrá alakítanánk át. Minden olyan cella, amely nem képes átalakítani, hibaállapotban lesz, és szűrni kell. Ez a forgatókönyv nem lehetséges az adatfolyam okozása esetén. |
| Table.Transpose | Nem támogatott |
| Table.Pivot | Nem támogatott |

## <a name="next-steps"></a>További lépések

További információ a rról, hogyan [hozhat létre vonó adatfolyamot.](wrangling-data-flow-tutorial.md)