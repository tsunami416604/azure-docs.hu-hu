---
title: Keresési átalakítás a leképezési adatfolyamban
description: Egy másik forrásból származó adatokra történő hivatkozás a leképezési adatforgalomban található keresési transzformáció használatával.
author: kromerm
ms.reviewer: daperlov
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/23/2020
ms.openlocfilehash: 672fecc7487a73909efa5b4247f4889bb47b7b7e
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594321"
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

## <a name="analyzing-matched-rows"></a>Egyező sorok elemzése

A keresési transzformáció után a függvény `isMatch()` használatával megtekintheti, hogy az adott sorokhoz tartozó keresés megfelel-e az egyes soroknak.

![Keresési minta](media/data-flow/lookup111.png "Keresési minta")

Erre a mintára példa a feltételes felosztású átalakítás használata a `isMatch()` függvényre való felosztásra. A fenti példában az egyező sorok a felső streamen haladnak át, a nem egyező sorok pedig az ```NoMatch``` adatfolyamon keresztül áramlanak.

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
* [Feltételes felosztású átalakítás](data-flow-conditional-split.md) ```isMatch()``` használata a paranccsal a sorok felosztása a megfeleltetés és a nem egyező értékek alapján
