---
title: Változó tevékenység beállítása Azure Data Factoryban
description: Megtudhatja, hogyan állíthatja be egy Data Factory-folyamatban definiált meglévő változó értékét a változó beállítása tevékenység használatával.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/10/2018
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.openlocfilehash: 88500ecbc56b34551a0cbd3ca94727ba4bbcda9f
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930645"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Változó tevékenység beállítása Azure Data Factoryban

A változó beállítása tevékenységgel egy Data Factory-folyamatban definiált karakterlánc, bool vagy Array típusú változó értékét állíthatja be.

## <a name="type-properties"></a>Típus tulajdonságai

Tulajdonság | Leírás | Szükséges
-------- | ----------- | --------
név | A tevékenység neve a folyamatban | Igen
leírás | A tevékenység működését leíró szöveg | nem
type | A tevékenység típusa SetVariable | igen
érték | A megadott változó beállításához használt karakterlánc-literál vagy kifejezés objektum értéke | igen
variableName | A tevékenység által beállított változó neve | igen


## <a name="next-steps"></a>Következő lépések
A Data Factory által támogatott kapcsolódó vezérlési folyamatokkal kapcsolatos tevékenységek ismertetése: 

- [Változó tevékenységének hozzáfűzése](control-flow-append-variable-activity.md)
