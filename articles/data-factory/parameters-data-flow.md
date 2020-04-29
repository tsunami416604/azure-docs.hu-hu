---
title: Leképezési adatfolyamok paraméterezése
description: Ismerje meg, hogyan parametrizálja egy leképezési adatfolyamot a adatfeldolgozó-folyamatokból
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 82660cdb4ab6523bae7608fe3b071f20cb3603f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81419170"
---
# <a name="parameterizing-mapping-data-flows"></a>Leképezési adatfolyamok paraméterezése

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

A Azure Data Factory adatforgalmának leképezése támogatja a paraméterek használatát. A paramétereket definiálhatja az adatfolyam-definícióban, amelyet aztán használhat a kifejezésekben. A paraméterek értékeit a hívó folyamat állíthatja be az adatfolyam végrehajtása tevékenységen keresztül. Az adatáramlási tevékenység kifejezésekben az értékek beállítására három lehetőség áll rendelkezésre:

* Dinamikus érték beállítása a folyamat-vezérlési folyamat kifejezésének nyelvén
* Dinamikus érték beállítása az adatfolyam-kifejezés nyelvén
* A kifejezés nyelvének használata statikus literál érték megadásához

Ezzel a képességgel általános célú, rugalmas és újrahasznosítható adatfolyamatokat végezhet. Ezekkel a paraméterekkel parametrizálja az adatáramlási beállításokat és kifejezéseket.

## <a name="create-parameters-in-a-mapping-data-flow"></a>Paraméterek létrehozása egy leképezési adatfolyamban

Ha paramétereket szeretne adni az adatfolyamathoz, kattintson az adatáramlási vászon üres részére az általános tulajdonságok megtekintéséhez. A beállítások ablaktáblán egy **paraméter**nevű lap jelenik meg. Új paraméter létrehozásához válassza az **új** lehetőséget. Minden paraméterhez hozzá kell rendelnie egy nevet, ki kell választania egy típust, és opcionálisan meg kell adnia egy alapértelmezett értéket.

![Adatfolyam paramétereinek létrehozása](media/data-flow/create-params.png "Adatfolyam paramétereinek létrehozása")

## <a name="use-parameters-in-a-mapping-data-flow"></a>Paraméterek használata egy leképezési adatfolyamban 

A paraméterek bármely adatfolyam-kifejezésben szerepelhetnek. A paraméterek a $-vel kezdődnek, és nem változtathatók meg. Az elérhető paraméterek listáját a **Parameters (paraméterek** ) lapon találja a Expression builden belül.

![Adatfolyam paraméterének kifejezése](media/data-flow/parameter-expression.png "Adatfolyam paraméterének kifejezése")

Az **új paraméter** kiválasztásával és a név és a típus megadásával gyorsan hozzáadhat további paramétereket.

![Adatfolyam paraméterének kifejezése](media/data-flow/new-parameter-expression.png "Adatfolyam paraméterének kifejezése")

### <a name="passing-in-a-column-name-as-a-parameter"></a>Oszlop nevének átadása paraméterként

Egy általános minta egy oszlop neve paraméterként való továbbítása. A paraméterhez társított oszlopra való hivatkozáshoz használja a `byName()` függvényt. Ne felejtse el átadni az oszlopot a megfelelő típusra egy öntési `toString()`függvénnyel, például:.

Ha például egy paraméteren `columnName`alapuló karakterlánc-oszlopot szeretne leképezni, hozzáadhat egy származtatott oszlopot, amely egyenlő a `toString(byName($columnName))`következővel:.

![Oszlop nevének átadása paraméterként](media/data-flow/parameterize-column-name.png "Oszlop nevének átadása paramete")

## <a name="assign-parameter-values-from-a-pipeline"></a>Paraméterek értékének kiosztása egy folyamatból

Miután létrehozta az adatfolyamatot a paraméterekkel, végrehajthatja azt egy folyamaton az adatfolyam végrehajtása tevékenységgel. Miután hozzáadta a tevékenységet a folyamat vásznon, megjelenik a tevékenység **Paraméterek** lapján elérhető adatfolyam-paraméterek.

![Adatfolyam-paraméter beállítása](media/data-flow/parameter-assign.png "Adatfolyam-paraméter beállítása")

Ha a paraméter adattípusa karakterlánc, akkor a paraméterek értékének megadásához kattintson a szövegmezőre, és megadhat egy folyamatot vagy egy adatfolyam-kifejezést. Ha a folyamat kifejezés lehetőséget választja, a folyamat kifejezés panel jelenik meg. Ügyeljen arra `'@{<expression>}'`, hogy a folyamat függvényei szerepeljenek a karakterlánc-interpolációs szintaxison belül, például:

```'@{pipeline().RunId}'```

Ha a paraméter nem karakterlánc típusú, mindig az adatfolyam-kifejezés-szerkesztő jelenik meg. Itt megadhat bármely olyan kifejezést vagy literál értéket, amelyet szeretne, amely megfelel a paraméter adattípusának. Az alábbi példák az adatfolyamatok kifejezésére és a kifejezés-szerkesztőből származó szöveges karakterláncra mutatnak:

* ```toInteger(Role)```
* ```'this is my static literal string'```

Az egyes leképezési adatfolyamok a folyamat és az adatfolyam-kifejezés paramétereinek bármely kombinációját tartalmazhatják. 

![Adatfolyam-paraméterek mintája](media/data-flow/parameter-example.png "Adatfolyam-paraméterek mintája")



## <a name="next-steps"></a>További lépések
* [Adatfolyam-tevékenység végrehajtása](control-flow-execute-data-flow-activity.md)
* [Vezérlési folyamat kifejezései](control-flow-expression-language-functions.md)
