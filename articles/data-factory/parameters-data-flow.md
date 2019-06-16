---
title: Az Azure Data Factory-hozzárendelési adatok folyamat paraméterei
description: Ismerje meg, hogyan paraméterezni egy leképezési adatok folyamatot az adat-előállító folyamatok
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: af5f421cc3802f3a7ad44bb294f5066c32569f8b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082884"
---
# <a name="mapping-data-flow-parameters"></a>Leképezési adatok folyamat paraméterei

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Adatok hozzárendelése az adat-előállító folyamatok paraméterek használatát támogatja. Megadhat paramétereket, amelyek az folyamat definíciója, amely ezután használhatja a kifejezések teljes belül. A paraméterek majd beállítható úgy, hogy a hívó folyamat segítségével az adatok folyamat végrehajtása tevékenység. Az adatfolyam az értékek beállítása tevékenység-kifejezések három lehetősége van:

* A dinamikus érték beállításához használja a folyamat control flow kifejezés nyelve
* Data flow kifejezés nyelv használata a dinamikus érték beállítása
* Állandó szövegkonstans érték vagy kifejezés nyelve segítségével

Használja ezt a képességet, hogy az adatfolyamok, általános célú, rugalmas és újrahasználható. Akkor is parametrizálja folyamat beállítások és kifejezések ezekkel a paraméterekkel.

> [!NOTE]
> Folyamat control flow kifejezések használata, az adatok a flow paraméter karakterlánc típusú kell lennie.

* Adja hozzá a folyamatvásznon adatok folyamat végrehajtása tevékenység.
* Ha az adatfolyam paraméterekkel rendelkezik, kattintson a szövegmezőben adja meg a paraméter értéke a paraméterek mellé a paraméterek tab.* * elérhető paraméterek listáját láthatja.
* Kiválaszthatja, hogy a paraméter kifejezés a folyamat control flow kifejezésnyelveket n keresztül vagy a data flow kifejezéseket hozhat létre.

![Adatfolyam-paraméterek 3](media/data-flow/params3.png "adatfolyam paraméterek 3")

## <a name="create-parameters-in-data-flow"></a>Az adatfolyam paramétereinek létrehozása

![Adatfolyam-paraméterek 1](media/data-flow/params1.png "adatfolyam paraméterek 1")

Paramétereket ad hozzá az adatfolyamot, kattintson a data flow vászon általános tulajdonságainak megtekintéséhez a üres része. A beállítások panelen látni fogja a paraméterek nevű lapot. Az új gombra kattintva hozzon létre új paramétereket, majd állítható be a folyamatban, az átadott értékeket az adatok flow-bA. Adja meg a paraméter neve, és válassza ki az egyes paraméter.

Belül a flow kifejezés használhat a paraméterek használatával a folyamat a megadott értékeket. Paraméterek $ előtaggal kell kezdődnie, és nem módosíthatók. A Paraméterek lapján a Kifejezésszerkesztő belül elérhető paraméterek listáját is talál. Használhatja ezeket az értékeket a kifejezésekben, bár Ön nem jogosult új értékeket a paraméterekhez.

![Adatfolyam-paraméterek 2](media/data-flow/params2.png "adatfolyam paraméterek 2")

## <a name="set-data-flow-parameters-from-pipeline"></a>Adatok folyamatot a flow paraméterek beállítása

Ha létrehozta az adatfolyama paraméterekkel, most már futtathatja, hogy az adatfolyam egy folyamatot az adatok a folyamat végrehajtása tevékenység. Miután hozzáadja a tevékenység a folyamat tervezési vásznat, megjelenik az elérhető adatokat tartalmazó folyamat paramétereket a tevékenység-paraméterek beállítása lapon.

![Adatfolyam-paraméterek kifejezésnyelveket](media/data-flow/params4.png "adatfolyam paraméterek kifejezés nyelve")

Kattintva kitölthető paraméter értékét, a szövegmezőbe, akkor megjelenik a Data Flow Kifejezésszerkesztőt. Itt adhatja meg bármilyen kifejezés vagy a kívánt megfelelő a paraméter adattípusa konstansértékekkel. Az alábbiakban példákat data flow kifejezés és a egy konstans sztring a Kifejezésszerkesztő:

* ```toInteger(Role)```
* ```'this is my static literal string'```

Ha a paraméter adattípusa egy karakterláncot, majd ha szeretné, adja meg a folyamat vagy egy data flow kifejezés. Ha úgy dönt, hogy a folyamat kifejezés, akkor ehelyett megjelenik a folyamat kifejezés panel. Ügyeljen arra, hogy tartalmazzák a folyamat funkciók belül karakterlánc-interpolációs szintaxis használatával "@{<expression>}', például:

```'@{pipeline().RunId}'```

![A folyamat paramétereit adatmintából](media/data-flow/params5.png "adatmintából folyamat paraméterei")

## <a name="next-steps"></a>További lépések

* [Data flow tevékenység végrehajtása](control-flow-execute-data-flow-activity.md)
* [Ellenőrzési folyamat kifejezések](control-flow-expression-language-functions.md)
