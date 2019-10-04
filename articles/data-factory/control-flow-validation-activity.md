---
title: Érvényesítési tevékenység a Azure Data Factoryban | Microsoft Docs
description: Az érvényesítési tevékenység nem folytatja a folyamat végrehajtását mindaddig, amíg a felhasználó által megadott feltételekkel ellenőrzi a csatolt adatkészletet.
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
ms.openlocfilehash: 77fdab04e03429d135875cb2ef223e8c23d312a2
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141607"
---
# <a name="validation-activity-in-azure-data-factory"></a>Érvényesítési tevékenység a Azure Data Factoryban
A folyamat érvényesítésével biztosíthatja, hogy a folyamat csak akkor folytassa a végrehajtást, ha a csatolt adatkészlet hivatkozása már létezik, hogy az megfelel a megadott feltételeknek, vagy elérte az időtúllépést.


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
name | Az "Érvényesítés" tevékenység neve | Sztring | Igen |
type | Érvényesítésre kell beállítani. | Sztring | Igen |
adatkészlet | A tevékenység letiltja a végrehajtást, amíg nem ellenőrizte, hogy az adatkészlet-hivatkozás létezik, és megfelel a megadott feltételeknek, vagy elérte az időtúllépést. A megadott adatkészletnek támogatnia kell a "MinimumSize" vagy a "ChildItems" tulajdonságot. | Adatkészlet-hivatkozás | Igen |
timeout | Megadja a futtatni kívánt tevékenység időtúllépését. Ha nincs megadva érték, az alapértelmezett érték 7 nap ("7.00:00:00"). A formátum d. óó: PP: SS | Sztring | Nem |
aludni | Az érvényesítési kísérletek közötti késleltetés másodpercben. Ha nincs megadva érték, az alapértelmezett érték 10 másodperc. | Integer | Nem |
childItems | Ellenőrzi, hogy a mappában van-e alárendelt elem. Állítható be a-True értékre: Ellenőrizze, hogy a mappa létezik-e, és hogy vannak-e elemek. A rendszer letiltja, amíg legalább egy elem szerepel a mappában, vagy elérte az időtúllépési értéket.-FALSE: Ellenőrizze, hogy a mappa létezik-e, és hogy üres-e. Blokkolja a mappa üres vagy az időtúllépési érték eléréséig. Ha nincs megadva érték, a tevékenység letiltja, amíg a mappa nem létezik, vagy amíg az időtúllépés el nem éri. | Logikai | Nem |
minimumSize | A fájl minimális mérete bájtban megadva. Ha nincs megadva érték, az alapértelmezett érték 0 bájt | Integer | Nem |


## <a name="next-steps"></a>További lépések
Tekintse meg a Data Factory által támogatott egyéb vezérlési folyamatokat:

- [If Condition tevékenység](control-flow-if-condition-activity.md)
- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
- [Until tevékenység](control-flow-until-activity.md)
