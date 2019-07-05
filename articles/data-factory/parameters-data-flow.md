---
title: Az Azure Data Factory-folyamat Adatparaméterek leképezése
description: Ismerje meg, hogyan paraméterezni egy leképezési adatok folyamatot az adat-előállító folyamatok
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 0a7140f70db78c8511f3c4da00b2f9c11c368163
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477705"
---
# <a name="mapping-data-flow-parameters"></a>Data Flow paraméterek leképezése

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Leképezés adatáramlás az Azure Data Factory támogatja a paraméterek használatával. Megadhat paramétereket, amelyek az folyamat definíciója, amely ezután használhatja a kifejezések teljes belül. A paraméter érték a hívó folyamat segítségével az adatok folyamat végrehajtása tevékenység állítható be. Az értékeket beállítani az adatfolyam tevékenység kifejezések három lehetősége van:

* A dinamikus érték beállításához használja a folyamat control flow kifejezés nyelve
* Data flow kifejezés nyelv használata a dinamikus érték beállítása
* Állandó szövegkonstans érték vagy kifejezés nyelve segítségével

Használja ezt a képességet, hogy az adatfolyamok, általános célú, rugalmas és újrahasználható. Akkor is parametrizálja folyamat beállítások és kifejezések ezekkel a paraméterekkel.

> [!NOTE]
> Folyamat control flow kifejezések használata, az adatok a flow paraméter karakterlánc típusú kell lennie.

## <a name="create-parameters-in-mapping-data-flow"></a>Az adatfolyam leképezési paraméterek létrehozása

Paramétereket ad hozzá az adatfolyamot, kattintson a data flow vászon általános tulajdonságainak megtekintéséhez a üres része. A beállítások panelen megjelenik egy "Parameters" nevű lapot. Az "Új" gombra kattintva hozzon létre egy új paramétert. Az egyes paraméterekhez tartozó Ön kell rendeljen egy nevet, válasszon egy típust és opcionálisan beállíthatja az alapértelmezett érték.

![Adatfolyam paramétereinek létrehozása](media/data-flow/create-params.png "paraméterek adatfolyam létrehozása")

Paraméterek, amellyel bármely flow-kifejezésben. Paraméterek $ előtaggal kell kezdődnie, és nem módosíthatók. A Kifejezésszerkesztő "Parameters" lapján elérhető paraméterek listáját megtalálja.

![Adatfolyam paraméterkifejezésben](media/data-flow/parameter-expression.png "adatfolyam paraméter kifejezés")

## <a name="use-parameters-in-your-data-flow"></a>Paraméterek használata az adatok folyamatban

* Az átalakítási kifejezések belül paraméterértékeket is használhatja. A paraméterlistából paraméterek lapján található a Kifejezésszerkesztőben. ![Adatfolyam-paraméterek használata](media/data-flow/params9.png "paraméterek használatát adatfolyam")

* Paramétereket is használhatók a dinamikus értékek beállítása a forrás és fogadó-átalakítási beállítások. Konfigurálható mezőket belül kattint, látni fogja a "Dinamikus contect hozzáadása" hivatkozást jelennek meg. Nincs kattintva léphet egy Kifejezésszerkesztő használható paraméterek használatához a dinamikus értékeket. ![Adatfolyam dinamikus tartalom](media/data-flow/params6.png "adatfolyam dinamikus tartalom")

## <a name="set-mapping-data-flow-parameters-from-pipeline"></a>A folyamat adatfolyam leképezési paraméterek beállítása

Miután létrehozta az adatfolyama paraméterekkel, futtathatja azt egy folyamatot az adatok a folyamat végrehajtása tevékenység. Miután hozzáadta a tevékenység a folyamatvásznon, megjelenik az elérhető adatokat tartalmazó folyamat paramétereket a tevékenység "Parameters" lapon.

![Egy adatfolyam-paraméter](media/data-flow/parameter-assign.png "egy adatfolyam-paraméter")

Ha a paraméter adattípusa karakterlánc, amikor kattint, a szövegmező paraméterértékek beállítása, ha szeretné, adja meg a folyamat vagy egy data flow kifejezés. Ha úgy dönt, hogy a folyamat kifejezés, akkor megjelenik a folyamat kifejezés panel. Ügyeljen arra, hogy tartalmazzák a folyamat funkciók belül karakterlánc-interpolációs szintaxis használatával "@{<expression>}', például:

```'@{pipeline().RunId}'```

Ha a paraméter nem karakterlánc típusú, hogy mindig megjelenik a Data Flow Kifejezésszerkesztő. Itt adhatja meg bármilyen kifejezés vagy konstansértékekkel, hogy szeretné, hogy a paraméter adattípusa megegyezik. Az alábbiakban példákat data flow kifejezés és a egy konstans sztring a Kifejezésszerkesztő:

* ```toInteger(Role)```
* ```'this is my static literal string'```

Mindegyik leképezési adatfolyam is rendelkezik-e a folyamat és az adatok folyamat kifejezés paraméterek. 

![A folyamat paramétereit adatmintából](media/data-flow/parameter-example.png "adatmintából folyamat paraméterei")



## <a name="next-steps"></a>További lépések
* [Data flow tevékenység végrehajtása](control-flow-execute-data-flow-activity.md)
* [Ellenőrzési folyamat kifejezések](control-flow-expression-language-functions.md)
