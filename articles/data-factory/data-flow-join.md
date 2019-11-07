---
title: Az átalakítás összekapcsolása Azure Data Factory leképezési adatfolyamban
description: Két adatforrásból származó adatok összevonása az Azure Data Factory-leképezési folyamat illesztési átalakításának használatával
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 4680804017a9b08248bb41ff999c6ba6371e99c8
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73675915"
---
# <a name="join-transformation-in-mapping-data-flow"></a>Az átalakítás összekapcsolása a leképezési adatfolyamban

Az illesztési transzformáció használatával két forrásból vagy streamből származó adatok egyesíthetők egy leképezési adatfolyamban. A kimeneti adatfolyam magában foglalja az összekapcsolási feltételtől függően mindkét forrás összes oszlopát. 

## <a name="join-types"></a>Illesztési típusok

Az adatforgalom leképezése jelenleg öt különböző illesztési típust támogat.

### <a name="inner-join"></a>Belső illesztés

A belső illesztés csak a táblákkal egyező értékeket tartalmazó sorok kimenetét jeleníti meg.

### <a name="left-outer"></a>Bal oldali külső

A bal oldali külső illesztés visszaadja az összes sort a bal oldali adatfolyamból, és a megfelelő streamből egyeztetett rekordokat. Ha a bal oldali adatfolyamból egy sor nem egyezik, a jobb oldali adatfolyamból származó kimeneti oszlopok NULL értékre vannak állítva. A kimenet a belső illesztés és a bal oldali adatfolyam nem egyező sorai által visszaadott sorokból áll.

### <a name="right-outer"></a>Jobb oldali külső

A jobb oldali külső illesztés a jobb oldali adatfolyamból származó összes sort visszaadja a bal oldali adatfolyamból. Ha a jobb oldali adatfolyamból származó sor nem egyezik, a bal oldali adatfolyam kimeneti oszlopai NULL értékre vannak állítva. A kimenet a belső illesztés által visszaadott sorok, valamint a jobb oldali adatfolyamban található nem egyező sorokból áll.

### <a name="full-outer"></a>Teljes külső

A teljes külső illesztés kimenete a két oldalról származó összes oszlopot és sort az oszlopok esetében NULL értékekkel nem egyezik.

### <a name="cross-join"></a>Keresztbe illesztés

A Cross JOIN egy feltétel alapján a két stream termékeit adja vissza. Ha olyan feltételt használ, amely nem egyenlő, adja meg az egyéni kifejezést a kereszt illesztési feltételként. A kimeneti adatfolyam az illesztési feltételnek megfelelő összes sor lesz. Ha olyan Descartes-szorzatot szeretne létrehozni, amely minden sor kombinációját kiírja, adja meg a `true()` csatlakoztatási feltételként.

## <a name="configuration"></a>Konfiguráció

1. Válassza ki, hogy melyik adatfolyamot kívánja csatlakoztatni a **megfelelő stream** legördülő menüben.
1. Válassza ki az **illesztés típusát**
1. Válassza ki, hogy mely kulcs oszlopokat szeretné összekapcsolni a csatlakozás feltételéhez. Alapértelmezés szerint az adatforgalom az egyes adatfolyamok egy oszlopa között keresi az egyenlőséget. Számított érték alapján történő összehasonlításhoz vigye az egérmutatót az oszlop legördülő menüjére, és válassza a **számított oszlop**lehetőséget.

![Csatlakozás az átalakításhoz](media/data-flow/join.png "Csatlakozás")

## <a name="optimizing-join-performance"></a>Az illesztési teljesítmény optimalizálása

Az egyesítési illesztéstől eltérően az olyan eszközökhöz, mint a SSIS, az illesztési átalakítás nem kötelező egyesítő illesztési művelet. Az illesztési kulcsok nem igénylik a rendezést. Az illesztési művelet a Spark-beli optimális illesztési műveleten alapul, vagy szórásos vagy térképes illesztés.

![Összekapcsolási átalakítás optimalizálása](media/data-flow/joinoptimize.png "Csatlakozás optimalizálása")

Ha az adatfolyamok egyike vagy mindkettő illeszkedik a munkavégző csomópont memóriába, a teljesítmény optimalizálása érdekében engedélyezze a **szórást** az optimalizálás lapon. Az összekapcsolási művelettel is újraparticionálhatja az adatait, hogy az jobban illeszkedik a memóriába egy munkavégzőn.

## <a name="self-join"></a>Önálló csatlakozás

Ha önmagához szeretne csatlakoztatni egy adatfolyamot, alias egy meglévő streamet egy kiválasztott átalakítással. Hozzon létre egy új ágat a transzformáció melletti plusz ikonra kattintva, majd válassza az **új ág**lehetőséget. Adjon hozzá egy Select transzformációt az eredeti stream aliasához. Vegyen fel egy JOIN transzformációt, és válassza ki az eredeti streamet a **bal oldali streamként** , a **jobb oldali**adatfolyamként pedig válassza az átalakítás lehetőséget.

![Önálló csatlakozás](media/data-flow/selfjoin.png "Önálló csatlakozás")

## <a name="testing-join-conditions"></a>Csatlakozási feltételek tesztelése

Az adatelőnézetsel rendelkező illesztési átalakítások hibakeresési módban történő tesztelésekor használjon egy ismert adat kis készletét. Nagy adatkészletből származó mintavételezési sorok esetében nem lehet előre jelezni, hogy mely sorok és kulcsok lesznek beolvasva a teszteléshez. Az eredmény nem determinisztikus, ami azt jelenti, hogy az illesztési feltételek nem adnak vissza egyezést.

## <a name="data-flow-script"></a>Adatfolyam-parancsfájl

### <a name="syntax"></a>Szintaxis

```
<leftStream>, <rightStream>
    join(
        <conditionalExpression>,
        joinType: { 'inner'> | 'outer' | 'left_outer' | 'right_outer' | 'cross' }
        broadcast: { 'none' | 'left' | 'right' | 'both' }
    ) ~> <joinTransformationName>
```

### <a name="inner-join-example"></a>Példa belső illesztésre

Az alábbi példa egy `JoinMatchedData` nevű összekapcsolási átalakítás, amely a stream `TripData` és a jobb oldali stream `TripFare`t veszi át.  Az illesztési feltétel az a kifejezés `hack_license == { hack_license} && TripData@medallion == TripFare@medallion && vendor_id == { vendor_id} && pickup_datetime == { pickup_datetime}`, amely igaz értéket ad vissza, ha az egyes adatfolyamok `hack_license`, `medallion`, `vendor_id`és `pickup_datetime` oszlopok szerepelnek. A `joinType` `'inner'`. Csak a bal oldali streamben engedélyezzük a szórást, így `broadcast` érték `'left'`.

Az Data Factory UX-ben ez az átalakítás az alábbi képhez hasonlóan néz ki:

![Példa csatlakoztatásra](media/data-flow/join-script1.png "Példa csatlakoztatásra")

Az átalakításhoz tartozó adatfolyam-szkript az alábbi kódrészletben található:

```
TripData, TripFare
    join(
        hack_license == { hack_license}
        && TripData@medallion == TripFare@medallion
        && vendor_id == { vendor_id}
        && pickup_datetime == { pickup_datetime},
        joinType:'inner',
        broadcast: 'left'
    )~> JoinMatchedData
```

### <a name="cross-join-example"></a>Példa a többhöz való csatlakozásra

Az alábbi példa egy `CartesianProduct` nevű összekapcsolási átalakítás, amely a stream `TripData` és a jobb oldali stream `TripFare`t veszi át. Ez a transzformáció két streamet vesz igénybe, és a soraik Descartes szorzatát adja vissza. Az illesztési feltétel `true()`, mert egy teljes Descartes-szorzatot eredményez. A `cross``joinType`. Csak a bal oldali streamben engedélyezzük a szórást, így `broadcast` érték `'left'`.

Az Data Factory UX-ben ez az átalakítás az alábbi képhez hasonlóan néz ki:

![Példa csatlakoztatásra](media/data-flow/join-script2.png "Példa csatlakoztatásra")

Az átalakításhoz tartozó adatfolyam-szkript az alábbi kódrészletben található:

```
TripData, TripFare
    join(
        true(),
        joinType:'cross',
        broadcast: 'left'
    )~> CartesianProduct
```

## <a name="next-steps"></a>További lépések

Az adategyesítést követően hozzon létre egy [származtatott oszlopot](data-flow-derived-column.md) [, és az](data-flow-sink.md) adatait egy célhely adattárba.
