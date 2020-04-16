---
title: Adatfolyam-parancsfájl leképezése
description: A Data Factory adatfolyam-parancsfájl-háttér nyelvének áttekintése
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/13/2020
ms.openlocfilehash: e0042960c25d58b72bc0ab884de5a2db62e566d9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413450"
---
# <a name="data-flow-script-dfs"></a>Adatfolyam-parancsfájl (DFS)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Az adatfolyam-parancsfájl (DFS) a kódolási nyelvhez hasonló mögöttes metaadat, amely a leképezési adatfolyamban szereplő átalakítások végrehajtására szolgál. Minden átalakítást olyan tulajdonságok sorozata képvisel, amelyek biztosítják a feladat megfelelő futtatásához szükséges információkat. A szkript látható és szerkeszthető az ADF-ből, ha a böngésző felhasználói felületének felső menüszalagján található "script" gombra kattint.

![Parancsfájl gomb](media/data-flow/scriptbutton.png "Parancsfájl gomb")

Például `allowSchemaDrift: true,` egy forrás átalakítás a szolgáltatás azt mondja, hogy a forrás adatkészlet összes oszlopot az adatfolyamban akkor is, ha azok nem szerepelnek a séma vetülete.

## <a name="use-cases"></a>Használati esetek
A DFS-t a felhasználói felület automatikusan előállítja. A Parancsfájl gombra kattintva megtekintheti és testreszabhatja a parancsfájlt. Parancsfájlokat is létrehozhat az ADF felhasználói felületén kívül, majd adja át a PowerShell-parancsmagba. Összetett adatfolyamok hibakeresésekor egyszerűbben szállhat meg a parancsfájl-kód mögött, ahelyett, hogy a folyamatok felhasználói felületének grafikonábrázolását utána lenne.

Íme néhány példa használati esetekre:
- Programozott módon számos, meglehetősen hasonló adatáramlást hoz létre, azaz "kiirtja" az adatáramlásokat.
- A felhasználói felületen nehezen kezelhető vagy érvényesítési problémákat okozó összetett kifejezések.
- Hibakeresés és a végrehajtás során visszaadott különböző hibák jobb megértése.

Amikor létrehoz egy adatfolyam-parancsfájlt a PowerShell vagy egy API használatával, a formázott szöveget egyetlen sorba kell összecsuknia. A füleket és az új sorokat escape karakterként is megtarthatja. De a szöveget úgy kell formázni, hogy elférjen egy JSON tulajdonságban. Van egy gomb a script szerkesztő UI alján, amely formázza a forgatókönyvet, mint egy sort az Ön számára.

![a képernyő jobb oldalán található Másolás gombot](media/data-flow/copybutton.png "a képernyő jobb oldalán található Másolás gombot")

## <a name="how-to-add-transforms"></a>Hogyan adjunk átalakítások
Az átalakítások hozzáadása három alapvető lépést igényel: az alapvető átalakítási adatok hozzáadását, a bemeneti adatfolyam átirányítását, majd a kimeneti adatfolyam átirányítását. Ez látható a legegyszerűbb egy példában.
Tegyük fel, hogy egy egyszerű forrással kezdjük az adatáramlás takarásához, mint például:

```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Ha úgy döntünk, hogy adjunk egy származtatás átalakítás, először meg kell teremteni a `upperCaseTitle`mag transzformációs szöveget, amely egy egyszerű kifejezés, hogy adjunk egy új nagybetűs oszlop neve:
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

Ezután fogjuk a meglévő DFS-t, és hozzáadjuk az átalakítást:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

És most átirányítjuk a bejövő adatfolyamot, hogy azonosítjuk, melyik átalakulás `source1`után akarjuk az új átalakulást (ebben az esetben), és átmásoljuk az adatfolyam nevét az új átalakulásba:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Végül azonosítjuk az átalakulást, amelyet az új átalakulás után szeretnénk `sink1`eljönni, és a bemeneti adatfolyamot (ebben az esetben) az új átalakulás unk kimeneti adatfolyamának nevével helyettesítjük:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
deriveTransformationName sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="dfs-fundamentals"></a>Dfs alapjai
A DFS egy sor összekapcsolt átalakításból áll, beleértve a forrásokat, a fogadókat és számos más, amely új oszlopokat, szűrési adatokat, illesztési adatokat és még sok mást adhat hozzá. Általában a parancsfájl egy vagy több forrással kezdődik, amelyet számos átalakítás követ, és egy vagy több fogadóval végződik.

Források mind ugyanaz az alapvető konstrukció:
```
source(
  source properties
) ~> source_name
```

Például, egy egyszerű forrás három oszlop (movieId, cím, műfajok) lenne:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

A forrásokon kívüli összes átalakítás alapszerkezete megegyezik:
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

Például egy egyszerű származtatási transzformáció, amely egy oszlopot (címet) vesz fel, és nagybetűs verzióval felülírja, a következő:
```
source1 derive(
  title = upper(title)
) ~> derive1
```

És a mosogató nem séma egyszerűen:
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="script-snippets"></a>Parancsfájl-kódrészletek

A parancsfájlkódrészletek az adatfolyam-parancsfájl megosztható kódjai, amelyek segítségével megoszthatja az adatfolyamokat. Ez az alábbi videó aparancsfájlok kódrészletek használatáról és az adatfolyam-parancsfájl használatával másolja és illessze be a parancsfájl egyes részeit az adatfolyam-grafikonok mögé:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tA9b]


### <a name="aggregated-summary-stats"></a>Összesített összefoglaló statisztika
Adjon hozzá egy összesített átalakítást az "SummaryStats" nevű adatfolyamhoz, majd illessze be az alábbi kódot a parancsfájl összesített függvényéhez, és cserélje le a meglévő SummaryStats-ot. Ez általános mintát biztosít az adatprofil-összefoglaló statisztikákhoz.

```
aggregate(each(match(true()), $$+'_NotNull' = countIf(!isNull($$)), $$ + '_Null' = countIf(isNull($$))),
        each(match(type=='double'||type=='integer'||type=='short'||type=='decimal'), $$+'_stddev' = round(stddev($$),2), $$ + '_min' = min ($$), $$ + '_max' = max($$), $$ + '_average' = round(avg($$),2), $$ + '_variance' = round(variance($$),2)),
        each(match(type=='string'), $$+'_maxLength' = max(length($$)))) ~> SummaryStats
```
Az alábbi mintával megszámolhatja az adatok egyedi és különálló sorainak számát is. Az alábbi példa beilleszthető egy adatfolyamba a ValueDistAgg nevű összesített transzformációval. Ez a példa a "title" nevű oszlopot használja. Ügyeljen arra, hogy cserélje le a "cím" a karakterlánc oszlop az adatokban, hogy szeretné használni, hogy az értékszám.

```
aggregate(groupBy(title),
    countunique = count()) ~> ValueDistAgg
ValueDistAgg aggregate(numofunique = countIf(countunique==1),
        numofdistinct = countDistinct(title)) ~> UniqDist
```

### <a name="include-all-columns-in-an-aggregate"></a>Az összes oszlop felvétele összesítésbe
Ez egy általános összesítő minta, amely bemutatja, hogyan őrizheti meg a kimeneti metaadatok fennmaradó oszlopait az összesítések létrehozásakor. Ebben az esetben a ```first()``` funkciót használjuk, hogy kiválasszuk az első értéket minden oszlopban, amelynek neve nem "film". Ehhez hozzon létre egy DistinctRows nevű összesítő átalakítást, majd illessze be ezt a parancsfájlba a meglévő DistinctRows összesítő parancsfájl fölé.

```
aggregate(groupBy(movie),
    each(match(name!='movie'), $$ = first($$))) ~> DistinctRows
```

### <a name="create-row-hash-fingerprint"></a>Sorkivonat-ujjlenyomat létrehozása 
Ezzel a kóddal hozhat létre egy új ```DWhash``` származtatott oszlopot, amely három oszlopból álló kivonatot ```sha1``` hoz létre.

```
derive(DWhash = sha1(Name,ProductNumber,Color))
```

## <a name="next-steps"></a>További lépések

Az adatfolyamok felfedezése az [adatfolyamok áttekintése című cikkvel](concepts-data-flow-overview.md) kezdve
