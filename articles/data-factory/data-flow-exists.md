---
title: A Azure Data Factory-leképezési adatfolyamban található átalakítás
description: Meglévő sorok ellenőrzése a létező átalakítással Azure Data Factory leképezési adatfolyamban
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: a477eba246c4ebcfbd32e92f1fd30c301ea1cc5b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73676733"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>Létező átalakítás a leképezési adatfolyamban

A létező transzformáció egy sor-szűrési átalakítás, amely ellenőrzi, hogy az adatai más forrásban vagy adatfolyamban léteznek-e. A kimeneti adatfolyam tartalmazza a bal oldali stream összes olyan sorát, amely vagy létezik, vagy nem létezik a megfelelő streamben. A létező átalakítás a ```SQL WHERE EXISTS``` és a ```SQL WHERE NOT EXISTS```hoz hasonló.

## <a name="configuration"></a>Konfiguráció

1. Válassza ki, hogy melyik adatfolyamot kívánja megkeresni a **megfelelő stream** legördülő menüben.
1. Adja meg, hogy szeretné-e, ha a létező vagy nem létező **adattípust** szeretné használni.
1. Válassza ki, hogy szeretne-e **Egyéni kifejezést**használni.
1. Válassza ki, hogy mely fő oszlopokat kívánja összehasonlítani a meglévő feltételekkel. Alapértelmezés szerint az adatforgalom az egyes adatfolyamok egy oszlopa között keresi az egyenlőséget. Számított érték alapján történő összehasonlításhoz vigye az egérmutatót az oszlop legördülő menüjére, és válassza a **számított oszlop**lehetőséget.

![Létező beállítások](media/data-flow/exists.png "létezik 1")

### <a name="multiple-exists-conditions"></a>Több meglévő feltétel

Ha több oszlopot szeretne összehasonlítani az egyes adatfolyamokból, adjon hozzá egy új létező feltételt egy meglévő sor melletti plusz ikonra kattintva. Minden további feltétel egy "és" utasítással csatlakozik. A két oszlop összehasonlítása megegyezik a következő kifejezéssel:

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>Egyéni kifejezés

Ha olyan szabad formátumú kifejezést szeretne létrehozni, amely a "és a" és az "egyenlő" operátort is tartalmazza, válassza az **egyéni kifejezés** mezőt. Adjon meg egy egyéni kifejezést az adatfolyam-kifejezés-szerkesztőn keresztül a kék mezőre kattintva.

![Meglévő egyéni beállítások](media/data-flow/exists1.png "létezik egyéni")

## <a name="data-flow-script"></a>Adatfolyam-parancsfájl

### <a name="syntax"></a>Szintaxis

```
<leftStream>, <rightStream>
    exists(
        <conditionalExpression>,
        negate: { true | false },
        broadcast: {'none' | 'left' | 'right' | 'both'}
    ) ~> <existsTransformationName>
```

### <a name="example"></a>Példa

Az alábbi példa egy `checkForChanges` nevű átalakítás, amely a stream `NameNorm2` és a jobb oldali stream-`TypeConversions`t veszi át.  A létező feltétel a `NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region` kifejezés, amely igaz értéket ad vissza, ha az egyes streamek `EMPID` és `Region` oszlopa is megegyezik. A létezés ellenőrzésekor `negate` hamis. Az optimalizálás lapon nincs engedélyezve a szórás, így `broadcast` érték `'none'`.

Az Data Factory UX-ben ez az átalakítás az alábbi képhez hasonlóan néz ki:

![Létezik példa](media/data-flow/exists-script.png "Létezik példa")

Az átalakításhoz tartozó adatfolyam-szkript az alábbi kódrészletben található:

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'none'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>További lépések

Hasonló átalakítások [Keresés](data-flow-lookup.md) és [Csatlakozás](data-flow-join.md).
