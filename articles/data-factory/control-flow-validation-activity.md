---
title: Érvényesítési tevékenység az Azure Data Factoryban
description: Az érvényesítési tevékenység nem folytatja a folyamat végrehajtását, amíg a felhasználó által megadott bizonyos feltételekkel nem ellenőrzi a csatolt adatkészletet.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: f63c78c59d7d6be3c66ea0785389eff73e3bff60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73678363"
---
# <a name="validation-activity-in-azure-data-factory"></a>Érvényesítési tevékenység az Azure Data Factoryban
Az érvényesítés segítségével biztosíthatja, hogy a folyamat csak akkor folytatja a végrehajtást, ha a csatolt adatkészlet-hivatkozás létezik, hogy megfelel a megadott feltételeknek, vagy elérte az időtúlot.


## <a name="syntax"></a>Szintaxis

```json

{
    "name": "Validation_Activity",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_File",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "minimumSize": 20
    }
},
{
    "name": "Validation_Activity_Folder",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_Folder",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "childItems": true
    }
}

```


## <a name="type-properties"></a>Típus tulajdonságai

Tulajdonság | Leírás | Megengedett értékek | Kötelező
-------- | ----------- | -------------- | --------
név | Az "Érvényesítési" tevékenység neve | Sztring | Igen |
type | Az **Érvényesítés**beállításnak kell lennie. | Sztring | Igen |
Adatkészlet | A tevékenység mindaddig letiltja a végrehajtást, amíg nem érvényesítette ezt az adatkészlet-hivatkozást, és nem felel meg a megadott feltételeknek, vagy el nem érte az időtúltöltést. A megadott adatkészletnek támogatnia kell a "MinimumSize" vagy a "ChildItems" tulajdonságot. | Adatkészlet hivatkozása | Igen |
timeout | Megadja a futtatni kívánt tevékenység időtúllépését. Ha nincs megadva érték, az alapértelmezett érték 7 nap ("7.00:00:00"). A formátum d.hh:pp:ss | Sztring | Nem |
Alvás | Az ellenőrzési kísérletek közötti másodpercek késleltetése. Ha nincs megadva érték, az alapértelmezett érték 10 másodperc. | Egész szám | Nem |
childItems (gyermekelemek) | Ellenőrzi, hogy a mappában vannak-e gyermekelemek. Igaz értékre állítható: Ellenőrizze, hogy a mappa létezik-e, és hogy vannak-e elemek. Blokkok, amíg legalább egy elem nincs megad a mappában, vagy időtúllépési érték elérése.-false: Ellenőrizze, hogy a mappa létezik, és hogy üres. Blokkok, amíg a mappa üres, vagy amíg időtúllépési érték elérése. Ha nincs megadva érték, a tevékenység letiltásra kerül, amíg a mappa meg nem létezik, vagy amíg el nem éri az időtúlértéket. | Logikai | Nem |
minimumMéret | A fájl minimális mérete bájtban. Ha nincs megadva érték, az alapértelmezett érték 0 bájt | Egész szám | Nem |


## <a name="next-steps"></a>További lépések
Tekintse meg a Data Factory által támogatott egyéb vezérlési folyamattevékenységeket:

- [If Condition tevékenység](control-flow-if-condition-activity.md)
- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden egyes tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresstevékenységet](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
- [Until tevékenység](control-flow-until-activity.md)
