---
title: Létezik transzformáció az adatfolyam leképezésében
description: Meglévő sorok ellenőrzése az Azure Data Factory leképezési adatfolyamában az létező transzformáció használatával
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: a303c8fa1e23460fb906232eedb6bfb1930b4bc9
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606469"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>Létezik transzformáció az adatfolyam leképezésében

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A létezik transzformáció egy sorszűrési transzformáció, amely ellenőrzi, hogy az adatok léteznek-e más forrásban vagy adatfolyamban. A kimeneti adatfolyam tartalmazza a bal oldali adatfolyam minden olyan sorát, amely létezik, vagy nem létezik a jobb oldali adatfolyamban. A létezik transzformáció ```SQL WHERE NOT EXISTS```hasonló a hoz és ahoz. ```SQL WHERE EXISTS```

## <a name="configuration"></a>Konfiguráció

1. Válassza ki, hogy melyik adatfolyamot ellenőrzi a **jobb oldali adatfolyam** legördülő menüben.
1. Adja meg, hogy az adatok létezéséhez kell-e hozzájuk, vagy sem a **Létezés típus** beállításban.
1. Válassza ki, hogy szeretne-e **egyéni kifejezést.**
1. Válassza ki, hogy mely kulcsoszlopokat szeretné összehasonlítani a fennálló feltételekkel. Alapértelmezés szerint az adatfolyam az egyes adatfolyamok egy oszlopa közötti egyenlőséget keresi. Ha számított értéken keresztül szeretne összehasonlítani, mutasson az oszlop legördülő menüre, és válassza a **Számított oszlop lehetőséget.**

![Létező beállítások](media/data-flow/exists.png "létezik 1")

### <a name="multiple-exists-conditions"></a>Több létezik feltétel

Ha minden adatfolyamból több oszlopot szeretne összehasonlítani, adjon hozzá egy új létezési feltételt a meglévő sor melletti plusz ikonra kattintva. Minden további feltételhez egy "és" utasítás kapcsolódik. Két oszlop összehasonlítása megegyezik a következő kifejezéssel:

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>Egyéni kifejezés

Ha olyan szabadkézi kifejezést szeretne létrehozni, amely nem "és" és "egyenlő" operátorokat tartalmaz, jelölje be az **Egyéni kifejezés** mezőt. A kék mezőre kattintva írjon be egy egyéni kifejezést az adatfolyam-kifejezés szerkesztőjén keresztül.

![Létezik egyéni beállítások](media/data-flow/exists1.png "létezik egyéni")

## <a name="data-flow-script"></a>Adatfolyamszkript

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

Az alábbi példa egy `checkForChanges` létező átalakítás, `NameNorm2` amely `TypeConversions`bal és jobb streamet vesz igénybe.  A létezési `NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region` feltétel az a `EMPID` kifejezés, amely igaz értéket ad vissza, ha az egyes adatfolyamok `Region` oszlopai egyeznek. Ahogy a létezést `negate` ellenőrizzük, az hamis. Nem engedélyezzük a műsorszórást az Optimalizálás `broadcast` lapon, `'none'`így van értéke.

A Data Factory UX-ben ez az átalakítás az alábbi képre hasonlít:

![Létezik példa](media/data-flow/exists-script.png "Létezik példa")

Az átalakítás adatfolyam-parancsfájlja az alábbi kódrészletben található:

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'none'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>További lépések

Hasonló átalakítások a [Keresés](data-flow-lookup.md) és [a Csatlakozás](data-flow-join.md).
