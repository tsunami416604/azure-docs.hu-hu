---
title: Illesztés átalakítása az adatfolyam leképezésében
description: Két adatforrásból származó adatok kombinálása az Azure Data Factory leképezési adatfolyamának illesztési transzformációjával
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/02/2020
ms.openlocfilehash: 0c0e35f7f06afd0cafa4a1e353b7eda84ed226f2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413668"
---
# <a name="join-transformation-in-mapping-data-flow"></a>Illesztés átalakítása az adatfolyam leképezésében

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Az illesztési transzformációsegítségével két forrásból vagy adatfolyamból származó adatokat egyesíthet egy leképezési adatfolyamban. A kimeneti adatfolyam tartalmazza a két forrás összes oszlopát, amelyek illesztési feltétel alapján egyeztetve. 

## <a name="join-types"></a>Illesztési típusok

Az adatfolyamok leképezése jelenleg öt különböző illesztési típust támogat.

### <a name="inner-join"></a>Belső illesztés

A belső illesztés csak olyan sorokat ad ki, amelyek mindkét táblában egyező értékekkel rendelkeznek.

### <a name="left-outer"></a>Bal külső

A bal oldali laza illesztés a bal oldali adatfolyam összes sorát adja vissza, és a jobb oldali adatfolyamból származó rekordokat egyezteti. Ha a bal oldali adatfolyam egy sorának nincs egyezése, a jobb oldali adatfolyam kimeneti oszlopai NULL értékre vannak állítva. A kimenet egy belső illesztés által visszaadott sorok, valamint a bal oldali adatfolyam nem egyező sorai lesznek.

> [!NOTE]
> Az adatfolyamok által használt Spark-motor időnként meghibásodik a csatlakozási feltételekben lehetséges descartes-i termékek miatt. Ebben az esetben átválthat egyéni keresztillesztésre, és manuálisan megadhatja az illesztési feltételt. Ez lassabb teljesítményt eredményezhet az adatfolyamokban, mivel előfordulhat, hogy a végrehajtási motornak ki kell számítania a kapcsolat mindkét oldaláról az összes sort, majd szűrnie kell a sorokat.

### <a name="right-outer"></a>Jobb oldali külső

A jobb oldali külső illesztés a jobb oldali adatfolyam összes sorát adja vissza, és a bal oldali adatfolyamból származó egyező rekordokat. Ha a jobb oldali adatfolyam egyik sorának nincs egyezése, a bal oldali adatfolyam kimeneti oszlopai NULL értékre vannak állítva. A kimenet egy belső illesztés által visszaadott sorok lesznek, valamint a jobb oldali adatfolyam nem egyező sorai.

### <a name="full-outer"></a>Teljes külső

A teljes laza illesztés mindkét oldalról az összes oszlopot és sort kiadja, és null értékkel rendelkezik a nem egyező oszlopokhoz.

### <a name="custom-cross-join"></a>Egyéni keresztillesztés

A keresztillesztés feltétel alapján adja ki a két adatfolyam kereszttermékét. Ha nem egyenlőségi feltételt használ, adjon meg egy egyéni kifejezést keresztillesztési feltételként. A kimeneti adatfolyam az összes olyan sor lesz, amely megfelel az illesztési feltételnek.

Ezt az illesztési típust nem equi illesztésekhez és ```OR``` feltételekhez használhatja.

Ha explicit módon szeretne egy teljes descartes-i terméket készíteni, használja a Származtatott oszlop transzformációt a két független adatfolyammindegyikében az illesztés előtt, hogy hozzon létre egy szintetikus kulcsot, amely megfelel. Hozzon létre például egy új oszlopot ```SyntheticKey``` a Származtatott oszlopban minden meghívott adatfolyamban, és állítsa be a programra. ```1``` Ezután ```a.SyntheticKey == b.SyntheticKey``` egyéni illesztési kifejezésként használja.

> [!NOTE]
> Ügyeljen arra, hogy a bal és a jobb kapcsolat mindkét oldaláról legalább egy oszlopot vegyen fel egy egyéni keresztillesztésbe. Ha a keresztillesztéseket mindkét oldal oszlopai helyett statikus értékekkel hajtja végre, az a teljes adatkészlet teljes vizsgálatát eredményezi, ami az adatfolyam gyenge teljesítését eredményezi.

## <a name="configuration"></a>Konfiguráció

1. Válassza ki, hogy melyik adatfolyamhoz csatlakozik a **Jobb oldali adatfolyam** legördülő menüben.
1. Válassza ki a **Csatlakozás típusát**
1. Válassza ki, hogy mely kulcsoszlopokat szeretné megválasztani a feltételhez. Alapértelmezés szerint az adatfolyam az egyes adatfolyamok egy oszlopa közötti egyenlőséget keresi. Ha számított értéken keresztül szeretne összehasonlítani, mutasson az oszlop legördülő menüre, és válassza a **Számított oszlop lehetőséget.**

![Csatlakozás átalakításhoz](media/data-flow/join.png "Csatlakozás")

## <a name="optimizing-join-performance"></a>Az illesztési teljesítmény optimalizálása

Az egyesítési illesztéssel ellentétben az olyan eszközökben, mint az SSIS, az illesztés átalakítása nem kötelező egyesítési illesztési művelet. Az illesztési kulcsok nem igényelnek rendezést. Az illesztési művelet a Spark optimális illesztési művelete alapján történik, akár szórásos, akár térképoldali illesztés.

![Csatlakozás az átalakítás optimalizálásához](media/data-flow/joinoptimize.png "Csatlakozás optimalizálásához")

Ha az egyik vagy mindkét adatfolyam illeszkedik a munkavégző csomópont memóriájába, az Optimalizálás lapon a **Broadcast** engedélyezésével tovább optimalizálhatja a teljesítményt. Az illesztési művelet adatait újraparticionálhatja is, hogy azok jobban illeszkedjenek a dolgozónkénti memóriába.

## <a name="self-join"></a>Öncsatlakozó

Ha önmagával szeretne csatlakozni egy adatfolyamhoz, aliasként egy meglévő adatfolyamot egy kiválasztott átalakítással. Hozzon létre egy új ágat az átalakítás melletti plusz ikonra kattintva, és válassza az **Új ág**lehetőséget. Válasszon átalakítást adjon hozzá az eredeti adatfolyam aliasához. Illesztésátalakításahozzáadása, és válassza ki az eredeti adatfolyamot **bal oldali adatfolyamként,** a select átalakítást **pedig jobb oldali adatfolyamként.**

![Öncsatlakozó](media/data-flow/selfjoin.png "Öncsatlakozó")

## <a name="testing-join-conditions"></a>A csatlakozási feltételek tesztelése

Ha az illesztésátalakításokat adatteszteléssel teszteli hibakeresési módban, használjon ismert adatok egy kis készletét. Amikor sorokat mintavételez egy nagy adatkészletből, nem tudja előre, hogy mely sorok és kulcsok lesznek beolvasva tesztelésre. Az eredmény nem determinisztikus, ami azt jelenti, hogy a csatlakozási feltételek nem adnak vissza egyezést.

## <a name="data-flow-script"></a>Adatfolyamszkript

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

Az alábbi példa egy `JoinMatchedData` illesztési `TripData` transzformáció, `TripFare`amely bal és jobb streamet vesz igénybe.  Az illesztési `hack_license == { hack_license} && TripData@medallion == TripFare@medallion && vendor_id == { vendor_id} && pickup_datetime == { pickup_datetime}` feltétel az a `hack_license` `medallion`kifejezés, amely igaz értéket ad vissza, ha a , , `vendor_id`, és `pickup_datetime` az oszlopok minden adatfolyamban egyeznek. Az `joinType` `'inner'`is . Mi vagyunk, amely lehetővé teszi `broadcast` a `'left'`műsorszórás csak a bal oldali patak, így van értéke .

A Data Factory UX-ben ez az átalakítás az alábbi képre hasonlít:

![Példa a csatlakozáshoz](media/data-flow/join-script1.png "Példa a csatlakozáshoz")

Az átalakítás adatfolyam-parancsfájlja az alábbi kódrészletben található:

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

### <a name="custom-cross-join-example"></a>Egyéni keresztillesztési példa

Az alábbi példa egy `JoiningColumns` illesztési `LeftStream` transzformáció, `RightStream`amely bal és jobb streamet vesz igénybe. Ez az átalakítás két adatfolyamot vesz fel, és összefűzi az összes olyan sort, ahol az oszlop `leftstreamcolumn` nagyobb, mint az oszlop `rightstreamcolumn`. Az `joinType` `cross`is . A műsorszórás nincs `broadcast` engedélyezve, értéke `'none'`van.

A Data Factory UX-ben ez az átalakítás az alábbi képre hasonlít:

![Példa a csatlakozáshoz](media/data-flow/join-script2.png "Példa a csatlakozáshoz")

Az átalakítás adatfolyam-parancsfájlja az alábbi kódrészletben található:

```
LeftStream, RightStream
    join(
        leftstreamcolumn > rightstreamcolumn,
        joinType:'cross',
        broadcast: 'none'
    )~> JoiningColumns
```

## <a name="next-steps"></a>További lépések

Az adatok egyesítése után hozzon létre egy [származtatott oszlopot,](data-flow-derived-column.md) és az adatokat a céladattárba [süllyesztse.](data-flow-sink.md)
