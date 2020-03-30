---
title: A felkéselés átalakítása az adatfolyam leképezésében
description: Hivatkozási adatok egy másik forrásból a keresgő átalakítás használatával az adatfolyam leképezésében.
author: kromerm
ms.reviewer: daperlov
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/23/2020
ms.openlocfilehash: 78c6c1363af011a90865770d88c0037e50e958c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240412"
---
# <a name="lookup-transformation-in-mapping-data-flow"></a>A felkéselés átalakítása az adatfolyam leképezésében

A lookup transzformáció segítségével hivatkozzon az adatfolyam egy másik forrásából származó adatokra. A keresés átalakítása oszlopokat fűz az egyező adatokból a forrásadatokhoz.

A keresgélés átalakítása hasonlít a bal külső illesztéshez. Az elsődleges adatfolyam minden sora a kimeneti adatfolyamban fog létezni, és további oszlopok jelennek meg a felkereshető adatfolyamból. 

## <a name="configuration"></a>Konfiguráció

![A keresgazba– átalakítás](media/data-flow/lookup1.png "Keresés")

**Elsődleges adatfolyam:** A bejövő adatfolyam. Ez az adatfolyam megegyezik az illesztés bal oldalával.

**A következő keresgélő adatfolyam:** Az elsődleges adatfolyamhoz hozzáfűzött adatok. A hozzáadott adatokat a kapcsolati feltételek határozzák meg. Ez az adatfolyam megegyezik az illesztés jobb oldalával.

**Több sor egyeztetése:** Ha engedélyezve van, az elsődleges adatfolyamban több egyezést tartalmazó sor több sort ad vissza. Ellenkező esetben csak egy sor kerül visszaadásra a "Match on" feltétel alapján.

**Meccs bekapcsolva:** Csak akkor látható, ha a "Több sor egyeztetése" engedélyezve van. Válassza ki, hogy bármelyik sorban, az első egyezésben vagy az utolsó egyezésben van-e. Minden sor ajánlott, mivel végrehajtja a leggyorsabb. Ha az első vagy az utolsó sor van kijelölve, meg kell adnia a rendezési feltételeket.

**A következő keresgaszínállapotok:** Válassza ki, hogy mely oszlopokon szeretne egyeződni. Ha az egyenlőség feltétele teljesül, akkor a sorok egyeznek. Az alkalmazás rámutatása és a "Számított oszlop" lehetőséget választva az [adatfolyam-kifejezés nyelvével](data-flow-expression-functions.md)kinyerheti az értéket.

A keresgab-átalakítás csak az egyenlőségi egyezéseket támogatja. Ha úgy szeretné testreszabni a keresgabkifejezést, hogy az más operátorokat is tartalmazzon, például nagyobbakat, ajánlott [keresztillesztést használni az illesztéstranszformációban.](data-flow-join.md#custom-cross-join) A keresztillesztés elkerüli az esetleges descartes-i termékhibákat a végrehajtássorán.

Mindkét adatfolyam összes oszlopa szerepel a kimeneti adatokban. Ismétlődő vagy nem kívánt oszlopok eldobásához vegyen fel egy [kijelölési átalakítást](data-flow-select.md) a keresett átalakítás után. Az oszlopok eldobhatók vagy átnevezhetők a fogadó átalakítása során is.

## <a name="analyzing-matched-rows"></a>Egyező sorok elemzése

A keresés átalakítása után `isMatch()` a függvény segítségével ellenőrizheti, hogy a keresés megfelelt-e az egyes soroknak.

![Keress minta](media/data-flow/lookup111.png "Keress minta")

Egy példa erre a mintára a feltételes `isMatch()` felosztástranszformáció használatával a függvény felosztásához. A fenti példában az egyező sorok a felső adatfolyamon haladnak át, és a nem egyező sorok végigfolynak az ```NoMatch``` adatfolyamon.

## <a name="testing-lookup-conditions"></a>Vizsgálati vizsgálati feltételek

Ha a keresési átalakítást adatelőnézettel teszteli hibakeresési módban, használjon ismert adatok egy kis készletét. Amikor sorokat mintavételez egy nagy adatkészletből, nem tudja előre, hogy mely sorok és kulcsok lesznek beolvasva tesztelésre. Az eredmény nem determinisztikus, ami azt jelenti, hogy a csatlakozási feltételek nem adnak vissza egyezést.

## <a name="broadcast-optimization"></a>Közvetítés optimalizálása

Az Azure Data Factory leképezési adatfolyamok horizontális an-kimenő Spark-környezetekben hajthatók végre. Ha az adatkészlet elfér a munkavégző csomópont memóriaterületére, a műsorszórás engedélyezésével optimalizálható a keresmény teljesítménye.

![Közvetítési csatlakozás](media/data-flow/broadcast.png "Közvetítési csatlakozás")

A műsorszórás engedélyezése a teljes adatkészletet a memóriába löki. A csak néhány ezer sort tartalmazó kisebb adatkészletek esetében a műsorszórás nagyban javíthatja a keresett teljesítményt. Nagy adatkészletek esetén ez a beállítás memóriakiesést okozhat.

## <a name="data-flow-script"></a>Adatfolyamszkript

### <a name="syntax"></a>Szintaxis

```
<leftStream>, <rightStream>
    lookup(
        <lookupConditionExpression>,
        multiple: { true | false },
        pickup: { 'first' | 'last' | 'any' },  ## Only required if false is selected for multiple
        { desc | asc }( <sortColumn>, { true | false }), ## Only required if 'first' or 'last' is selected. true/false determines whether to put nulls first
        broadcast: { 'none' | 'left' | 'right' | 'both' }
    ) ~> <lookupTransformationName>
```
### <a name="example"></a>Példa

![A keresgazba– átalakítás](media/data-flow/lookup-dsl-example.png "Keresés")

A fenti kapcsolatkövetési konfiguráció adatfolyam-parancsfájlja az alábbi kódrészletben található.

```
SQLProducts, DimProd lookup(ProductID == ProductKey,
    multiple: false,
    pickup: 'first',
    asc(ProductKey, true),
    broadcast: 'none')~> LookupKeys
```
## 
További lépések

* Az [illesztés](data-flow-join.md) és [az már létező](data-flow-exists.md) átalakítások több adatfolyam-bemenetet is bevesznek
* Feltételes [felosztásos](data-flow-conditional-split.md) átalakítás ```isMatch()``` használata sorok felosztásához az egyező és nem egyező értékeken
