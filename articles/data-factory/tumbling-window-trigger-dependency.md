---
title: Tumbling ablakeseményindító-függőségek létrehozása
description: Ismerje meg, hogyan hozhat létre függőséget egy bukdácsoló ablak eseményindító az Azure Data Factoryban.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418796"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Függőség létrehozása átfedésmentes ablak eseményindítójához
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk lépéseket tartalmaz egy tumbling ablak eseményindító függőségének létrehozásához. Az Ablak váltási eseményindítóinak megjelenítése című témakörben talál általános tudnivalókat: [Hogyan hozhatunk létre bukdácsoló ablakeseményindítót.](how-to-create-tumbling-window-trigger.md)

Annak érdekében, hogy egy függőségi láncot hozzon létre, és győződjön meg arról, hogy az eseményindító végrehajtása csak az adat-előállító egy másik eseményindító sikeres végrehajtása után történik, használja ezt a speciális funkciót egy bukdácsoló ablakfüggőség létrehozásához.

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Függőség létrehozása a Data Factory felhasználói felületén

Az eseményindítókfüggőség létrehozásához válassza **az Eseményindító > Speciális > Új**lehetőséget, majd válassza ki azt az eseményindítót, amelytől a megfelelő eltolás és méret függ. Válassza **a Befejezés gombra,** és tegye közzé a függőségek érvénybe léptetéséhez szükséges adat-előállító módosításokat.

![Függőség létrehozása](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png "Függőség létrehozása")

## <a name="tumbling-window-dependency-properties"></a>Ablakfüggőségi tulajdonságok bukdácsolása

A függőséggel rendelkező, bukdácsoló ablakeseményindító a következő tulajdonságokkal rendelkezik:

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

Az alábbi táblázat a Tumbling Window-függőség meghatározásához szükséges attribútumok listáját tartalmazza.

| **Tulajdonság neve** | **Leírás**  | **Típus** | **Szükséges** |
|---|---|---|---|
| type  | Ebben a legördülő menüben az összes meglévő bukdácsoló ablakeseményindító megjelenik. Válassza ki azt az eseményindítót, amelytől függőséget szeretne igénybe venni.  | TumblingWindowTriggerDependencyReference vagy SelfDependencyTumblingWindowsTriggerReference | Igen |
| offset | A függőségi eseményindító eltolása. Adjon meg egy értéket az időtartam formátumában, és mind a negatív, mind a pozitív eltolás megengedett. Ez a tulajdonság kötelező, ha az eseményindító önmagától függ, és minden más esetben nem kötelező. Az önfüggőségnek mindig negatív eltolásnak kell lennie. Ha nincs megadva érték, az ablak megegyezik magával az eseményindítóval. | Időtartomány<br/>(óó:pp:ss) | Ön-függőség: Igen<br/>Egyéb: Nem |
| size | A függőségi zuhanás ablakának mérete. Adjon meg egy pozitív időtartomány-értéket. Ez a tulajdonság nem kötelező. | Időtartomány<br/>(óó:pp:ss) | Nem  |

> [!NOTE]
> A bukdácsoló ablakeseményindító legfeljebb két másik eseményindítótól függhet.

## <a name="tumbling-window-self-dependency-properties"></a>Az ablak önfüggőségi tulajdonságainak bukdácsolása

Olyan esetekben, amikor az eseményindító nem kell a következő ablak, amíg az előző ablak sikeresen befejeződött, hozzon létre egy önfüggőség. Az önfüggőségi eseményindító, amely az előző óra korábbi futtatásainak sikerétől függ, az alábbi tulajdonságokkal rendelkezik:

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

Az alábbiakban az esetek és a bukdácsoló ablakfüggőségi tulajdonságok használatának illusztrációit találjuk.

### <a name="dependency-offset"></a>Függőség eltolása

![Példa eltolásra](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png "Példa eltolásra")

### <a name="dependency-size"></a>Függőség mérete

![Példa méretre](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png "Példa méretre")

### <a name="self-dependency"></a>Önfüggőség

![Önfüggőség](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png "Önfüggőség")

### <a name="dependency-on-another-tumbling-window-trigger"></a>Függőség egy másik bukdácsoló ablak eseményindítójától

Napi telemetriai feldolgozási feladat, amely az elmúlt hét nap kimenetének összesítésétől függően egy másik napi feladattól függ, és hétnapos gördülő ablakfolyamokat hoz létre:

![Példa függőségi példákra](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png "Példa függőségi példákra")

### <a name="dependency-on-itself"></a>Függőség önmagától

Napi feladat, ahol nincsenek hiányosságok a feladat kimeneti adatfolyamaiban:

![Példa önfüggőségre](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png "Példa önfüggőségre")

Ha bemutatót szeretne készíteni arról, hogyan hozhat létre függő folyamatokat az Azure Data Factoryban a bukdácsoló ablakeseményindító használatával, tekintse meg az alábbi videót:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Create-dependent-pipelines-in-your-Azure-Data-Factory/player]

## <a name="monitor-dependencies"></a>Függőségek figyelése

Figyelheti a függőségi láncot és a megfelelő ablakokat az eseményindító futtatásfigyelési lapjáról. Keresse meg **a figyelési > eseményindító-futtatások .** A műveletek oszlopban újrafuttathatja az eseményindítót, vagy megtekintheti annak függőségeit.

![Eseményindító-futtatások monitorozása](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png "Eseményindító-futtatások monitorozása")

Ha a "Trigger függőségek megtekintése" gombra kattint, láthatja a függőségek állapotát. Ha a függőségi eseményindítók egyike meghibásodik, sikeresen újra kell futtatnia a függő eseményindító futtatásához. A bukdácsoló ablak eseményindító várni függőségek hét napig időtúllépés előtt.

![Függőségek figyelése](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png "Függőségek figyelése")

Ha egy vizuálisabb megtekintéséhez az eseményindító függőségi ütemezés, válassza a Gantt nézetet.

![Függőségek figyelése](media/tumbling-window-trigger-dependency/tumbling-window-dependency09.png "Függőségek figyelése")

## <a name="next-steps"></a>További lépések

* Tekintse [át a bukdácsoló ablakeseményindító létrehozásának áttekintése](how-to-create-tumbling-window-trigger.md)
