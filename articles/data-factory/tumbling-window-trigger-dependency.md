---
title: Átfedésmentes ablak eseményindító dependenciák létrehozása az Azure Data Factory |} A Microsoft Docs
description: Ismerje meg, az Azure Data Factoryban átfedésmentes ablakos eseményindító létrehozása függőség.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: douglasl
ms.openlocfilehash: c51c1056869cbd7741fae2ed1a554a7c794d1a39
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55967276"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Átfedésmentes ablak eseményindító függőség létrehozása

Ez a cikk lépéseit a függőség az átfedésmentes ablakos eseményindító létrehozása. Átfedésmentes ablakos eseményindítókról kapcsolatos általános információkért lásd: [átfedésmentes ablakos eseményindító létrehozása](how-to-create-tumbling-window-trigger.md).

Egy függőségi lánc létrehozásához, és győződjön meg arról, hogy a trigger csak egy eseményindítót, adat-előállító sikeres végrehajtása után hajtja végre, a speciális funkció használatával átfedésmentes ablak függőség létrehozása.

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Függőség létrehozása a Data Factory felhasználói felületén

Függőség egy eseményindító létrehozásához válassza **eseményindító > Speciális > új**, majd válassza az eseményindító, a megfelelő eltolás és mérete attól függ. Válassza ki **Befejezés** és függőségeinek érvénybe a data factory módosítások közzétételére.

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png)

## <a name="tumbling-window-dependency-properties"></a>Átfedésmentes ablak függőség tulajdonságai

Átfedésmentes ablak eseményindító függőség a következő tulajdonságokkal rendelkezik:

```json
{
    "name": "DemoTrigger",
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
            "startTime": "2018-10-04T00:00:00.000Z",
            "delay": "00:00:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": "-02:00:00",
                    "offset": "-01:00:00",
                    "referenceTrigger": {
                        "referenceName": "DemoDependency1",
                        "type": "TriggerReference"
                    }
                }
            ]
        }
    }
}
```

Az alábbi táblázatban az Átfedésmentes ablak függőség meghatározásához szükséges attribútumok listáját.

| **Tulajdonság neve** | **Leírás**  | **Típus** | **Kötelező** |
|---|---|---|---|
| Eseményindító  | Az összes a meglévő átfedésmentes ablakos eseményindítók jelennek meg a legördülő. Válassza ki az eseményindító függőségi kibontakozni.  | TumblingWindowTrigger | Igen |
| Eltolás | A függőségi eseményindító eltolását. Adjon meg egy értéket a span idő formátumban, és negatív és pozitív egyaránt eltolások engedélyezettek. Ez a paraméter kötelező, ha az eseményindító van, és minden egyéb esetben maga függően nem kötelező. Önkiszolgáló-függőség mindig kell egy negatív eltolás. | Időtartomány | Önkiszolgáló-függőség: Igen más: Nem |
| Ablakméret | A függőségi átfedésmentes ablak méretének. Adjon meg egy értéket span idő formátumban. Ez a paraméter nem kötelező. | Időtartomány | Nem  |
|||||

## <a name="tumbling-window-self-dependency-properties"></a>Átfedésmentes ablak önkiszolgáló-függőség tulajdonságai

A forgatókönyvekben, ahol az eseményindító kell nem folytassa a következő időszakban mindaddig, amíg az előző ablak sikeresen befejeződött hozhat létre egy önkiszolgáló-függőséget. Önkiszolgáló-függőség az eseményindító alábbi tulajdonságok fog rendelkezni:

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

Az alábbiakban a forgatókönyvek az ábrák és a használati átfedésmentes ablak függőség tulajdonságai.

## <a name="dependency-offset"></a>Függőségi eltolása

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png)

## <a name="dependency-size"></a>Függőségi mérete

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png)

## <a name="self-dependency"></a>Self-dependency

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png)

## <a name="usage-scenarios"></a>Felhasználási területek

### <a name="dependency-on-another-tumbling-window-trigger"></a>Függőség egy másik átfedésmentes ablakos eseményindító

Például függően az elmúlt hét nap összesíti egy másik napi feladat napi telemetriai feldolgozási feladat kimeneti és 7 napos gördülő ablak Streamek állít elő:

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png)

### <a name="dependency-on-itself"></a>Maga a függőség

Nem található a feladat kimeneti adatfolyamokat hézagok napi feladatok:

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png)

## <a name="monitor-dependencies"></a>Függőségek figyelése

Függőségi lánc nyomon követheti, és a megfelelő windows, a trigger által futtassa figyelési lapjának. Navigáljon a **figyelés > aktiválja a futtatások**.

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png)

Kattintson az összes a függő eseményindító-futtatások a kijelölt ablak a szeretne ikonra.

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png)

## <a name="next-steps"></a>További lépések

Felülvizsgálat [átfedésmentes ablakos eseményindító létrehozása](how-to-create-tumbling-window-trigger.md).