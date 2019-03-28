---
title: Érvényesítési tevékenység az Azure Data Factoryban |} A Microsoft Docs
description: Az érvényesítés tevékenység folytassa az a folyamat végrehajtását bizonyos feltételeknek, a felhasználó adja meg a csatolt adatkészlet ellenőrzi azt.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: shlo
ms.openlocfilehash: 46447bdbea93d1f99c5682cf878c2035e6f49b78
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522949"
---
# <a name="validation-activity-in-azure-data-factory"></a>Az Azure Data Factoryban érvényesítési tevékenység
Használhatja egy érvényesítési a folyamat a folyamat csak zavartalan végrehajtási, miután megtörtént a csatolt adatkészlet-hivatkozásban létezik, hogy megfelel-e a megadott feltételeknek megfelelő, vagy elérte az időkorlát.


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


## <a name="type-properties"></a>Tulajdonságok

Tulajdonság | Leírás | Megengedett értékek | Szükséges
-------- | ----------- | -------------- | --------
név | Az "Ellenőrzés" tevékenység neve | String | Igen |
type | Meg kell **érvényesítési**. | String | Igen |
Adatkészlet | Tevékenység futtatásának letiltása, amíg az adatkészlet-hivatkozásban érvényesített létezik, és, hogy megfelel-e a megadott feltételeknek megfelelő, vagy elérte az időtúllépési lesz. A megadott adatkészlet támogatnia kell a "MinimumSize" vagy "ChildItems" tulajdonság. | Adatkészlet-hivatkozásban | Igen |
timeout | Megadja a futtatni kívánt tevékenység időtúllépését. Ha nem ad meg értéket, az alapértelmezett érték: 7 nap ("7.00:00:00"). Formátuma d.hh:mm:ss | String | Nem |
Alvó állapot | A késleltetés, másodpercben ellenőrzési kísérletek között. Ha nem ad meg értéket, az alapértelmezett érték: 10 másodperc. | Egész szám | Nem |
childItems | Ellenőrzi, hogy a mappa rendelkezik-e a gyermekelemek. Adható meg az igaz: Ellenőrizze, hogy a mappa létezik-e, és arról, hogy vannak-e elemeket. Letiltja, amíg legalább egy elem szerepel a mappa vagy időtúllépési érték elérésekor.-false (hamis): Ellenőrizze, hogy létezik-e a mappát, és üres. Blokkok amíg mappa nem üres, vagy amíg időtúllépési érték elérésekor. Ha nem ad meg értéket, tevékenység le fogja tiltani, amíg a mappa létezik, vagy időtúllépését. | Logikai | Nem |
minimumSize | Minimális fájlméret (bájt). Ha nem ad meg értéket, alapértelmezett értéke 0 bájt | Egész szám | Nem |


## <a name="next-steps"></a>További lépések
Tekintse meg a többi Data Factory által támogatott átvitelvezérlési tevékenységek:

- [If Condition tevékenység](control-flow-if-condition-activity.md)
- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden egyes tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
- [Until tevékenység](control-flow-until-activity.md)
