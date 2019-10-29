---
title: Azure Data Factory leképezési adatfolyam paraméterei
description: Ismerje meg, hogyan parametrizálja egy leképezési adatfolyamot a adatfeldolgozó-folyamatokból
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 0a1051d67bf45e96f82833ef8190008204cdc90b
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "72387538"
---
# <a name="mapping-data-flow-parameters"></a>Az adatfolyam paramétereinek leképezése



A Azure Data Factory adatforgalmának leképezése támogatja a paraméterek használatát. A paramétereket definiálhatja az adatfolyam-definícióban, amelyet aztán használhat a kifejezésekben. A paraméterek értékeit a hívó folyamat állíthatja be az adatfolyam végrehajtása tevékenységen keresztül. Az adatáramlási tevékenység kifejezésekben az értékek beállítására három lehetőség áll rendelkezésre:

* Dinamikus érték beállítása a folyamat-vezérlési folyamat kifejezésének nyelvén
* Dinamikus érték beállítása az adatfolyam-kifejezés nyelvén
* A kifejezés nyelvének használata statikus literál érték megadásához

Ezzel a képességgel általános célú, rugalmas és újrahasznosítható adatfolyamatokat végezhet. Ezekkel a paraméterekkel parametrizálja az adatáramlási beállításokat és kifejezéseket.

> [!NOTE]
> A folyamat-vezérlési folyamat kifejezésének használatához az adatfolyam-paraméternek karakterlánc típusúnak kell lennie.

## <a name="create-parameters-in-mapping-data-flow"></a>Paraméterek létrehozása a leképezési adatfolyamban

Ha paramétereket szeretne adni az adatfolyamathoz, kattintson az adatáramlási vászon üres részére az általános tulajdonságok megtekintéséhez. A beállítások ablaktáblán egy "parameters" nevű lap jelenik meg. Új paraméter létrehozásához kattintson az új gombra. Minden paraméterhez hozzá kell rendelnie egy nevet, ki kell választania egy típust, és opcionálisan meg kell adnia egy alapértelmezett értéket.

![Adatfolyam paramétereinek létrehozása](media/data-flow/create-params.png "Adatfolyam paramétereinek létrehozása")

A paraméterek bármely adatfolyam-kifejezésben használhatók. A paraméterek a $-vel kezdődnek, és nem változtathatók meg. A parameters (paraméterek) lapon megtalálja a kifejezés-szerkesztőben elérhető paraméterek listáját.

![Adatfolyam paraméterének kifejezése](media/data-flow/parameter-expression.png "Adatfolyam paraméterének kifejezése")

## <a name="use-parameters-in-your-data-flow"></a>Paraméterek használata az adatfolyamban

* Az átalakítási kifejezéseken belül paramétereket is használhat. A parameters (paraméterek) lapon megtalálja a paramétereket a Kifejezésszerkesztő területen. ![Adatfolyam paramétereinek használata](media/data-flow/params9.png "Use adatfolyam-paraméterek ")

* A paraméterek a forrás és a fogadó átalakítási beállítások dinamikus értékeinek konfigurálására is használhatók. Ha a konfigurálható mezőkön belülre kattint, megjelenik a "dinamikus megvet hozzáadása" hivatkozás. A gombra kattintva megtekintheti a kifejezés-szerkesztőt, ahol paramétereket használhat a dinamikus értékek használatára. ![Adatfolyam dinamikus tartalma](media/data-flow/params6.png "DATA-folyamat dinamikus tartalma ")

## <a name="set-mapping-data-flow-parameters-from-pipeline"></a>Leképezési adatfolyam paramétereinek beállítása a folyamatból

Miután létrehozta az adatfolyamatot a paraméterekkel, végrehajthatja azt egy folyamaton az adatfolyam végrehajtása tevékenységgel. Miután hozzáadta a tevékenységet a folyamat vásznon, megjelenik a tevékenység "parameters" lapján elérhető adatfolyam-paraméterek.

![Adatfolyam-paraméter beállítása](media/data-flow/parameter-assign.png "Adatfolyam-paraméter beállítása")

Ha a paraméter adattípusa karakterlánc, akkor a paraméterek értékének megadásához kattintson a szövegmezőre, és megadhat egy folyamatot vagy egy adatfolyam-kifejezést. Ha a folyamat kifejezés lehetőséget választja, a folyamat kifejezés panel jelenik meg. Győződjön meg arról, hogy az `'@{<expression>}'` értékkel együtt tartalmazza a folyamat függvényeit karakterlánc-interpolációs szintaxisban, például:

```'@{pipeline().RunId}'```

Ha a paraméter nem karakterlánc típusú, mindig az adatfolyam-kifejezés-szerkesztő jelenik meg. Itt megadhat bármely olyan kifejezést vagy literál értéket, amelyet szeretne, amely megfelel a paraméter adattípusának. Az alábbi példák az adatfolyamatok kifejezésére és a kifejezés-szerkesztőből származó szöveges karakterláncra mutatnak:

* ```toInteger(Role)```
* ```'this is my static literal string'```

Az egyes leképezési adatfolyamok a folyamat és az adatfolyam-kifejezés paramétereinek bármely kombinációját tartalmazhatják. 

![Adatfolyam-paraméterek mintája](media/data-flow/parameter-example.png "Adatfolyam-paraméterek mintája")



## <a name="next-steps"></a>Következő lépések
* [Adatfolyam-tevékenység végrehajtása](control-flow-execute-data-flow-activity.md)
* [Vezérlési folyamat kifejezései](control-flow-expression-language-functions.md)
