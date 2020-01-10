---
title: A sorok átalakításának módosítása a leképezési adatfolyamban
description: Az adatbázis céljának frissítése az Alter Row Transformation használatával a leképezési adatforgalomban
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/08/2020
ms.openlocfilehash: 0798a3f9ab45ce68086681e7aea96deeb9639f94
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834542"
---
# <a name="alter-row-transformation-in-mapping-data-flow"></a>A sorok átalakításának módosítása a leképezési adatfolyamban

Az Alter sort Transformation paranccsal szúrhatja be a sorokba az INSERT, DELETE, Update és upsert szabályzatokat. Egy-a-többhöz feltételeket adhat hozzá kifejezésként. Ezeket a feltételeket prioritási sorrendben kell megadni, mivel minden egyes sor az első megfeleltetési kifejezésnek megfelelő szabályzattal lesz megjelölve. Ezek a feltételek egy sor (vagy sor) beszúrását, frissítését, törlését vagy upserted eredményezik. Az Alter Row a DDL-& a DML-műveleteket is előkészítheti az adatbázison.

![Módosítási sor beállításai](media/data-flow/alter-row1.png "Módosítási sor beállításai")

Az Alter sor átalakítások csak adatbázis-vagy CosmosDB-tárolókban működnek az adatfolyamban. A sorokhoz rendelt műveletek (INSERT, Update, DELETE, upsert) nem lépnek fel a hibakeresési munkamenetek során. Futtasson egy végrehajtási adatfolyamati tevékenységet egy folyamaton belül, hogy meghozza a módosítási sor szabályzatait az adatbázis tábláiban.

## <a name="specify-a-default-row-policy"></a>Alapértelmezett sor szabályzatának meghatározása

Hozzon létre egy Alter sort átalakítást, és határozzon meg egy `true()`feltételt tartalmazó sort. Minden olyan sor meg lesz jelölve, amely nem felel meg a korábban definiált kifejezéseknek. Alapértelmezés szerint minden olyan sor meg lesz jelölve, amely nem felel meg a feltételes kifejezéseknek `Insert`.

![Módosítási sor házirendje](media/data-flow/alter-row4.png "Módosítási sor házirendje")

> [!NOTE]
> Az összes sor egyetlen házirenddel való megjelöléséhez létrehozhat egy feltételt az adott szabályzathoz, és a feltételt `true()`ként adhatja meg.

## <a name="view-policies-in-data-preview"></a>Szabályzatok megtekintése az adatelőnézetben

[Hibakeresési mód](concepts-data-flow-debug-mode.md) használatával megtekintheti az Alter Row-szabályzatok eredményeit az adatelőnézet ablaktáblán. Az Alter Row Transformation adatelőnézete nem hoz létre DDL-vagy DML-műveleteket a célhelyen.

![Módosítási sor házirendjei](media/data-flow/alter-row3.png "Módosítási sor házirendjei")

Minden módosítási sor házirendjét egy ikon jelöli, amely jelzi, hogy bekövetkezik-e az INSERT, a Update, a upsert vagy a Deleted művelet. A felső fejléc azt mutatja, hogy az egyes szabályzatok hány sort érintenek az előzetes verzióban.

## <a name="allow-alter-row-policies-in-sink"></a>Módosítási sor szabályzatának engedélyezése a fogadóban

Ahhoz, hogy az Alter Row-szabályzatok működjenek, az adatfolyamnak egy adatbázisba vagy egy Cosmos-fogadóba kell írnia. A fogadó **Beállítások** lapján engedélyezze, hogy az adott fogadó számára engedélyezett legyen a módosítási sor szabályzata.

![Módosítási sor fogadója](media/data-flow/alter-row2.png "Módosítási sor fogadója")

 Az alapértelmezett viselkedés csak beszúrások engedélyezése. Ha engedélyezni szeretné a frissítéseket, a upsert vagy a törlést, jelölje be az adott feltételnek megfelelő fogadó jelölőnégyzetét. Ha a frissítések, a upsert vagy a törlések engedélyezve vannak, meg kell adnia, hogy a fogadó mely kulcs oszlopait kell megegyeznie.

> [!NOTE]
> Ha a lapkák, a frissítések vagy a upsert módosítja a céltábla sémáját a fogadóban, az adatfolyam sikertelen lesz. Ha módosítani szeretné a célként megadott sémát az adatbázisban, válassza a tábla **újbóli létrehozása** lehetőséget a tábla műveletként. Ezzel eldobásra kerül, és újból létrehozza a táblát az új séma-definícióval.

## <a name="data-flow-script"></a>Adatfolyamszkript

### <a name="syntax"></a>Szintaxis

```
<incomingStream>
    alterRow(
           insertIf(<condition>?),
           updateIf(<condition>?),
           deleteIf(<condition>?),
           upsertIf(<condition>?),
        ) ~> <alterRowTransformationName>
```

### <a name="example"></a>Példa

Az alábbi példa egy `CleanData` nevű módosítási sor átalakítása, amely egy bejövő stream `SpecifyUpsertConditions`, és három módosítási sor feltételt hoz létre. Az előző átalakításban egy `alterRowCondition` nevű oszlop lesz kiszámítva, amely meghatározza, hogy a rendszer beszúrja, frissíti vagy törli-e a sort az adatbázisban. Ha az oszlop értéke olyan karakterlánc-értékkel rendelkezik, amely megfelel az Alter Row szabálynak, akkor azt a rendszer hozzárendeli a szabályzathoz.

Az Data Factory UX-ben ez az átalakítás az alábbi képhez hasonlóan néz ki:

![Módosítható sor – példa](media/data-flow/alter-row4.png "Módosítható sor – példa")

Az átalakításhoz tartozó adatfolyam-szkript az alábbi kódrészletben található:

```
SpecifyUpsertConditions alterRow(insertIf(alterRowCondition == 'insert'),
    updateIf(alterRowCondition == 'update'),
    deleteIf(alterRowCondition == 'delete')) ~> AlterRow
```

## <a name="next-steps"></a>Következő lépések

A módosítási sor átalakítását követően érdemes lehet [az adatait egy célhely adattárba menteni](data-flow-sink.md).
