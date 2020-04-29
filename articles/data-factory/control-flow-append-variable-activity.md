---
title: Változó tevékenység hozzáfűzése Azure Data Factory
description: Megtudhatja, hogyan állíthatja be a változó hozzáfűzése tevékenységet úgy, hogy értéket adjon hozzá egy Data Factory folyamatban definiált meglévő tömb-változóhoz.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.date: 10/09/2018
ms.openlocfilehash: 5d74cd0fcd524f00d79eb3fbab386c602a413766
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414211"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Változó tevékenység hozzáfűzése Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
A változó hozzáfűzése tevékenységgel hozzáadhat egy értéket egy Data Factory folyamatban definiált meglévő tömb változóhoz.

## <a name="type-properties"></a>Típus tulajdonságai

Tulajdonság | Leírás | Kötelező
-------- | ----------- | --------
név | A tevékenység neve a folyamatban | Igen
leírás | A tevékenység működését leíró szöveg | nem
type | A tevékenység típusa AppendVariable | igen
érték | A megadott változóhoz való hozzáfűzéshez használt karakterlánc-literál vagy kifejezés objektum értéke | igen
variableName | Annak a változónak a neve, amely tevékenység alapján módosul, a változónak "Array" típusúnak kell lennie. | igen

## <a name="next-steps"></a>További lépések
A Data Factory által támogatott kapcsolódó vezérlési folyamatokkal kapcsolatos tevékenységek ismertetése: 

- [Változó tevékenység beállítása](control-flow-set-variable-activity.md)
