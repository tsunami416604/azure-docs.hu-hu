---
title: Leképezési adatfolyamok paraméterezése
description: További információ az adat-előállító folyamatokból származó leképezési adatfolyam paraméterezéséről
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: c717d9ab44493d15589030073cd2ab260ef52e1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760215"
---
# <a name="parameterizing-mapping-data-flows"></a>Leképezési adatfolyamok paraméterezése

Az Azure Data Factory adatfolyamainak leképezése támogatja a paraméterek használatát. Paramétereket definiálhat az adatfolyam-definícióban, amelyeket aztán a kifejezések ben használhat. A paraméterértékeket a hívó folyamat állíthatja be az adatfolyam-végrehajtás tevékenységen keresztül. Az adatfolyam-tevékenység kifejezésekben három lehetőség közül választhat:

* Dinamikus érték beállítása a folyamatvezérlési folyamat kifejezés nyelvén
* Dinamikus érték beállítása az adatfolyam-kifejezés nyelvén
* Statikus literális érték beállítása a kifejezésnyelv bármelyikének beállításához

Ezzel a funkcióval általános, rugalmas és újrafelhasználhatóvá teheti az adatfolyamokat. Ezekkel a paraméterekkel paraméterezheti az adatfolyam-beállításokat és kifejezéseket.

## <a name="create-parameters-in-a-mapping-data-flow"></a>Paraméterek létrehozása leképezési adatfolyamban

Ha paramétereket szeretne hozzáadni az adatfolyamhoz, kattintson az adatfolyam vászon üres részére az általános tulajdonságok megtekintéséhez. A beállítások ablaktáblán megjelenik egy **Parameter**nevű lap. Új paraméter létrehozásához válassza az **Új** lehetőséget. Minden paraméterhez hozzá kell rendelnie egy nevet, ki kell választania egy típust, és szükség esetén meg kell állítania egy alapértelmezett értéket.

![Adatfolyam-paraméterek létrehozása](media/data-flow/create-params.png "Adatfolyam-paraméterek létrehozása")

## <a name="use-parameters-in-a-mapping-data-flow"></a>Paraméterek használata leképezési adatfolyamban 

A paraméterek rekretárda bármely adatfolyam-kifejezésben megtalálhatók. A paraméterek $ -mal kezdődnek, és nem módosíthatók. A rendelkezésre álló paraméterek listáját a Kifejezésszerkesztőben, a **Paraméterek** lapon találja.

![Adatfolyam paraméterkifejezése](media/data-flow/parameter-expression.png "Adatfolyam paraméterkifejezése")

Az **Új paraméter** kiválasztásával, a név és a típus megadásával gyorsan hozzáadhat további paramétereket.

![Adatfolyam paraméterkifejezése](media/data-flow/new-parameter-expression.png "Adatfolyam paraméterkifejezése")

### <a name="passing-in-a-column-name-as-a-parameter"></a>Oszlopnév megadása paraméterként

A gyakori minta az, hogy az oszlopnevet paraméterértékként adja át. A paraméterhez társított oszlopra való `byName()` hivatkozáshoz használja a függvényt. Ne felejtse el az oszlopot a megfelelő `toString()`típusra vetni egy öntési funkcióval, például .

Ha például egy paraméter `columnName`en alapuló karakterláncoszlopot szeretne leképezni, hozzáadhat `toString(byName($columnName))`egy származtatott oszlop átalakítást, amely megegyezik a.-val.

![Oszlopnév megadása paraméterként](media/data-flow/parameterize-column-name.png "Oszlopnév átadása parametaként")

## <a name="assign-parameter-values-from-a-pipeline"></a>Paraméterértékek hozzárendelése csővezetékből

Miután létrehozta az adatfolyamot paraméterekkel, végrehajthatja azt egy folyamatból az adatfolyam-tevékenység végrehajtása segítségével. Miután hozzáadta a tevékenységet a folyamatvászonhoz, megjelennek a rendelkezésre álló adatfolyam-paraméterek a tevékenység **Paraméterei** lapján.

![Adatfolyam-paraméter beállítása](media/data-flow/parameter-assign.png "Adatfolyam-paraméter beállítása")

Ha a paraméter adattípusa karakterlánc, amikor a szövegmezőre kattint a paraméterértékek beállításához, megadhatja a folyamat vagy az adatfolyam-kifejezés megadását. Ha a folyamatkifejezés lehetőséget választja, megjelenik a folyamatkifejezés panel. Győződjön meg arról, hogy a `'@{<expression>}'`csővezeték-függvényeket a karakterlánc interpolációs szintaxisában a használatával kell szerepeltetni, például:

```'@{pipeline().RunId}'```

Ha a paraméter nem karakterlánc típusú, mindig megjelenik az Adatfolyam-kifejezésszerkesztő. Itt bármilyen olyan kifejezést vagy konstans értéket megadhat, amely megfelel a paraméter adattípusának. Az alábbiakban példákat talál az adatfolyam-kifejezésre és a kifejezésszerkesztő literális karakterláncára:

* ```toInteger(Role)```
* ```'this is my static literal string'```

Minden leképezési adatfolyam a folyamat- és adatfolyam-kifejezés paramétereinek bármilyen kombinációjával rendelkezhet. 

![Adatfolyam-paraméterek mintája](media/data-flow/parameter-example.png "Adatfolyam-paraméterek mintája")



## <a name="next-steps"></a>További lépések
* [Adatfolyam-tevékenység végrehajtása](control-flow-execute-data-flow-activity.md)
* [Folyamatkifejezések vezérlése](control-flow-expression-language-functions.md)
