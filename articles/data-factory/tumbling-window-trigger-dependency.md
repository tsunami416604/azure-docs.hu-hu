---
title: Kieséses ablak trigger függőségeinek létrehozása
description: Megtudhatja, hogyan hozhat létre függőséget egy kieséses ablakos triggerből Azure Data Factoryban.
services: data-factory
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/29/2019
ms.openlocfilehash: 39ea8dda0fd823d3061b2cb29e1c548f99281c82
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81418796"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Függőség létrehozása átfedésmentes ablak eseményindítójához
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk bemutatja, hogyan hozhat létre függőséget egy kieséses ablakos triggeren. Az ablakos eseményindítókkal kapcsolatos általános információkért lásd: [a kieséses ablakos eseményindító létrehozása](how-to-create-tumbling-window-trigger.md).

Függőségi lánc létrehozásához és annak ellenőrzéséhez, hogy egy trigger csak egy másik trigger sikeres végrehajtása után fusson az adatgyárban, ezzel a speciális funkcióval hozzon létre egy ablakos függőséget.

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Függőség létrehozása a Data Factory felhasználói felületen

Ha függőséget szeretne létrehozni egy triggerhez, válassza az **aktiválás > speciális > új**lehetőséget, majd válassza ki a megfelelő eltolással és mérettel függő triggert. Válassza a **Befejezés** lehetőséget, és tegye közzé a függőségek érvénybe léptetéséhez szükséges adatfeldolgozó-módosításokat.

![Függőség létrehozása](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png "Függőség létrehozása")

## <a name="tumbling-window-dependency-properties"></a>Ablak függőségi tulajdonságainak kiesése

A függőséggel rendelkező kieséses ablakos trigger a következő tulajdonságokkal rendelkezik:

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": <<Started/Stopped/Disabled - readonly>>,
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": <<datetime>>,
            "endTime": <<datetime – optional>>,
            "delay": <<timespan – optional>>,
            "maxConcurrency": <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                "intervalInSeconds": <<int>>,
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan – optional>>,
                    "referenceTrigger": {
                        "referenceName": "MyTumblingWindowDependency1",
                        "type": "TriggerReference"
                    }
                },
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan>>
                }
            ]
        }
    }
}
```

Az alábbi táblázat tartalmazza az ablak függőségének definiálásához szükséges attribútumok listáját.

| **Tulajdonság neve** | **Leírás**  | **Típus** | **Szükséges** |
|---|---|---|---|
| type  | Ebben a legördülő ablakban minden meglévő ablak-eseményindító megjelenik. Válassza ki a függőség bekapcsolásához szükséges triggert.  | TumblingWindowTriggerDependencyReference vagy SelfDependencyTumblingWindowTriggerReference | Igen |
| offset | A függőségi trigger eltolása. Adja meg az időtartomány formátumú értéket, és a negatív és a pozitív eltolás is engedélyezett. Ez a tulajdonság akkor kötelező, ha az trigger saját magától függ, és minden más esetben nem kötelező. Az önfüggőségnek mindig negatív eltolásnak kell lennie. Ha nincs megadva érték, az ablak ugyanaz, mint maga az trigger. | Időtartomány<br/>(óó: PP: SS) | Önálló függőség: igen<br/>Egyéb: nem |
| size | A függőséget jelző ablak mérete Adjon meg egy pozitív TimeSpan értéket. Ez a tulajdonság nem kötelező. | Időtartomány<br/>(óó: PP: SS) | Nem  |

> [!NOTE]
> A kihelyezett ablakos eseményindítók legfeljebb két másik eseményindítótól függnek.

## <a name="tumbling-window-self-dependency-properties"></a>A kiesési ablak önfüggőségi tulajdonságai

Olyan helyzetekben, amikor az trigger nem folytatható a következő ablakba, amíg az előző ablak sikeresen be nem fejeződik, hozzon létre egy önfüggőséget. Az előző HR-en belüli korábbi futtatások sikerességét függő önfüggőségi trigger az alábbi tulajdonságokkal fog rendelkezni:

```json
{
    "name": "DemoSelfDependency",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00Z",
            "delay": "00:01:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": "01:00:00",
                    "offset": "-01:00:00"
                }
            ]
        }
    }
}
```
## <a name="usage-scenarios-and-examples"></a>Használati forgatókönyvek és példák

Alább láthatók a forgatókönyvek és az ablak függőségi tulajdonságainak használata.

### <a name="dependency-offset"></a>Függőségi eltolás

![Eltolási példa](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png "Eltolási példa")

### <a name="dependency-size"></a>Függőség mérete

![Példa a méretre](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png "Példa a méretre")

### <a name="self-dependency"></a>Önálló függőség

![Önálló függőség](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png "Önálló függőség")

### <a name="dependency-on-another-tumbling-window-trigger"></a>Függőség egy másik kiesést jelző ablakos triggertől

A napi telemetria-feldolgozási feladatok attól függően, hogy az elmúlt hét nap folyamán egy másik napi feladatot összesítenek, és hét napos időszakot állítanak elő:

![Függőségi példa](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png "Függőségi példa")

### <a name="dependency-on-itself"></a>Függőség önmagában

A feladatokhoz tartozó kimeneti adatfolyamok hiánya nélküli napi feladatok:

![Saját függőségi példa](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png "Saját függőségi példa")

Ahhoz, hogy a Azure Data Factory függő folyamatokat hozzon létre a kieséses ablakos triggerrel, tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Create-dependent-pipelines-in-your-Azure-Data-Factory/player]

## <a name="monitor-dependencies"></a>Függőségek figyelése

A függőségi láncot és a megfelelő Windows-t a trigger futtatásának figyelése lapról követheti nyomon. Navigáljon a **figyelés > triggerek futtatásához**. A műveletek oszlopban újra futtathatja a triggert, vagy megtekintheti annak függőségeit.

![Eseményindító-futtatások monitorozása](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png "Eseményindító-futtatások monitorozása")

Ha a "trigger függőségeinek megtekintése" gombra kattint, megtekintheti a függőségek állapotát. Ha a függőségi eseményindítók egyike meghibásodik, a függő eseményindító futtatásához sikeresen újra kell futtatnia azt. A kiugró ablakos trigger az időtúllépés előtt hét napig megvárja a függőségeket.

![Függőségek figyelése](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png "Függőségek figyelése")

Az trigger függőségi ütemtervének megjelenítéséhez kattintson a Gantt-nézetre.

![Függőségek figyelése](media/tumbling-window-trigger-dependency/tumbling-window-dependency09.png "Függőségek figyelése")

## <a name="next-steps"></a>További lépések

* [A bukdácsoló ablakos trigger létrehozásának](how-to-create-tumbling-window-trigger.md) áttekintése
