---
title: Változó tevékenység beállítása Azure Data Factoryban | Microsoft Docs
description: Megtudhatja, hogyan állíthatja be egy Data Factory-folyamatban definiált meglévő változó értékét a változó beállítása tevékenység használatával.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/10/2018
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.openlocfilehash: cfe6dd63234a7750fe01614d6f1b38bb7cce1adb
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142445"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Változó tevékenység beállítása Azure Data Factoryban

A változó beállítása tevékenységgel egy Data Factory-folyamatban definiált karakterlánc, bool vagy Array típusú változó értékét állíthatja be.

## <a name="type-properties"></a>Típus tulajdonságai

Tulajdonság | Leírás | Kötelező
-------- | ----------- | --------
name | A tevékenység neve a folyamatban | Igen
description | A tevékenység működését leíró szöveg | nem
type | A tevékenység típusa SetVariable | igen
value | A megadott változó beállításához használt karakterlánc-literál vagy kifejezés objektum értéke | igen
variableName | A tevékenység által beállított változó neve | igen


## <a name="next-steps"></a>További lépések
A Data Factory által támogatott kapcsolódó vezérlési folyamatokkal kapcsolatos tevékenységek ismertetése: 

- [Változó tevékenységének hozzáfűzése](control-flow-append-variable-activity.md)
