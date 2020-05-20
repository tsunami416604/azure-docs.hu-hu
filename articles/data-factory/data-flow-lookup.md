---
title: Keresési átalakítás a leképezési adatfolyamban
description: Egy másik forrásból származó adatokra történő hivatkozás a leképezési adatforgalomban található keresési transzformáció használatával.
author: kromerm
ms.reviewer: daperlov
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/15/2020
ms.openlocfilehash: 59c7a34e975a53226b032827feae436202c8fa30
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683318"
---
# <a name="lookup-transformation-in-mapping-data-flow"></a>Keresési átalakítás a leképezési adatfolyamban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A keresési transzformáció használatával hivatkozhat egy adatfolyamban található másik forrásból származó adatokra. A keresési átalakítás hozzáfűzi az egyező adatokból származó oszlopokat a forrásadatok adataihoz.

A keresési transzformáció hasonló a bal külső illesztéshez. Az elsődleges adatfolyamból származó összes sor a kimeneti adatfolyamban található, és a keresési adatfolyam további oszlopaival is rendelkezik. 

## <a name="configuration"></a>Konfiguráció

![Keresési transzformáció](media/data-flow/lookup1.png "Keresés")

**Elsődleges stream:** A bejövő adatfolyam. Ez az adatfolyam megegyezik a csatlakozás bal oldalával.

**Keresési stream:** Az elsődleges adatfolyamhoz fűzött adatforrások. A felvenni kívánt adatértékeket a keresési feltételek határozzák meg. Ez az adatfolyam egyenértékű a csatlakozás jobb oldalával.

**Több sor egyeztetése:** Ha engedélyezve van, az elsődleges adatfolyamban több egyezést tartalmazó sor több sort ad vissza. Ellenkező esetben csak egyetlen sor lesz visszaadva a "Match on" feltétel alapján.

**Egyezés:** Csak akkor látható, ha nincs bejelölve a "több sor egyeztetése" beállítás. Válassza ki, hogy az összes sor, az első egyezés vagy az utolsó egyezés egyezik-e. Minden sor ajánlott, mert a leggyorsabb végrehajtást hajtja végre. Ha az első sor vagy az utolsó sor van kiválasztva, meg kell adnia a rendezési feltételeket.

**Keresési feltételek:** Válassza ki, hogy mely oszlopok egyeznek meg. Ha az egyenlőségi feltétel teljesül, a sorok egyezésnek tekintendők. Vigye a kurzort, és válassza a "számított oszlop" lehetőséget egy érték kinyeréséhez az [adatfolyam kifejezésének nyelvével](data-flow-expression-functions.md).

A keresési átalakítás csak az esélyegyenlőségi egyezéseket támogatja. Ha testre szeretné szabni a keresési kifejezést úgy, hogy más operátorokat is tartalmazzon, például a nagyobbat, mint a, a [JOIN transzformációban érdemes kereszt illesztést](data-flow-join.md#custom-cross-join)használni. A Cross JOIN utasítással elkerülhetők az esetleges Descartes-féle hibák a végrehajtás során.

Mindkét stream összes oszlopa szerepel a kimeneti adatok között. Ismétlődő vagy nem kívánt oszlopok eldobásához vegyen fel egy [Select transzformációt](data-flow-select.md) a keresési transzformáció után. Az oszlopok elhelyezhetők vagy átnevezhető egy fogadó átalakításban.

### <a name="non-equi-joins"></a>Nem összekapcsolható illesztések

Ha feltételes operátort szeretne használni, például nem egyenlő (! =) vagy nagyobb, mint (>) a keresési feltételekben, módosítsa a kezelő legördülő listát a két oszlop között. A nem megfelelő illesztésekhez szükséges, hogy a két stream közül legalább az egyiket a **rögzített** szórás használatával lehessen közvetíteni az **optimalizálás** lapon.

![Nem-extrák keresése](media/data-flow/non-equi-lookup.png "Nem-extrák keresése")

## <a name="analyzing-matched-rows"></a>Egyező sorok elemzése

A keresési transzformáció után a függvény használatával `isMatch()` megtekintheti, hogy az adott sorokhoz tartozó keresés megfelel-e az egyes soroknak.

![Keresési minta](media/data-flow/lookup111.png "Keresési minta")

Erre a mintára példa a feltételes felosztású átalakítás használata a függvényre való felosztásra `isMatch()` . A fenti példában az egyező sorok a felső streamen haladnak át, a nem egyező sorok pedig az ```NoMatch``` adatfolyamon keresztül áramlanak.

## <a name="testing-lookup-conditions"></a>Keresési feltételek tesztelése

Ha hibakeresési módban végzi a keresési átalakítást az adatelőnézet használatával, az ismert adat kis készletét használja. Nagy adatkészletből származó mintavételezési sorok esetében nem lehet előre jelezni, hogy mely sorok és kulcsok lesznek beolvasva a teszteléshez. Az eredmény nem determinisztikus, ami azt jelenti, hogy az illesztési feltételek nem adnak vissza egyezést.

## <a name="broadcast-optimization"></a>Szórás optimalizálása

![Szórásos csatlakozás](media/data-flow/broadcast.png "Szórásos csatlakozás")

Az illesztések, a keresések és a meglévő átalakítások esetében, ha az egyik vagy mindkét adatfolyam a feldolgozó csomóponti memóriába illeszkedik, a **szórás**engedélyezésével optimalizálhatja a teljesítményt. Alapértelmezés szerint a Spark-motor automatikusan eldönti, hogy az egyik oldalt kívánja-e közvetíteni. A közvetíteni kívánt oldal manuális kiválasztásához válassza a **rögzített**lehetőséget.

Nem ajánlott letiltani a szórást a **kikapcsolási** lehetőséggel, kivéve, ha az illesztések időtúllépési hibákkal futnak.

## <a name="data-flow-script"></a>Adatfolyamszkript

### <a name="syntax"></a>Szintaxis

```
<leftStream>, <rightStream>
    lookup(
        <lookupConditionExpression>,
        multiple: { true | false },
        pickup: { 'first' | 'last' | 'any' },  ## Only required if false is selected for multiple
        { desc | asc }( <sortColumn>, { true | false }), ## Only required if 'first' or 'last' is selected. true/false determines whether to put nulls first
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <lookupTransformationName>
```
### <a name="example"></a>Példa

![Keresési transzformáció](media/data-flow/lookup-dsl-example.png "Keresés")

A fenti keresési konfiguráció adatáramlási parancsfájlja az alábbi kódrészletben található.

```
SQLProducts, DimProd lookup(ProductID == ProductKey,
    multiple: false,
    pickup: 'first',
    asc(ProductKey, true),
    broadcast: 'auto')~> LookupKeys
```
## 
További lépések

* A [JOIN](data-flow-join.md) és a [meglévő](data-flow-exists.md) átalakítások egyszerre több stream-bemenetet is igénybe vehetik
* [Feltételes felosztású átalakítás](data-flow-conditional-split.md) használata a paranccsal a ```isMatch()``` sorok felosztása a megfeleltetés és a nem egyező értékek alapján
