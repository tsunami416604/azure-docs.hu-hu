---
title: Változó tevékenység beállítása Azure Data Factoryban
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
ms.openlocfilehash: 2589e56d3170b235f31d07f902eb75c749e8573f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73679304"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Változó tevékenység beállítása Azure Data Factoryban

A változó beállítása tevékenységgel egy Data Factory-folyamatban definiált karakterlánc, bool vagy Array típusú változó értékét állíthatja be.

## <a name="type-properties"></a>Típus tulajdonságai

Tulajdonság | Leírás | Kötelező
-------- | ----------- | --------
név | A tevékenység neve a folyamatban | Igen
leírás | A tevékenység működését leíró szöveg | nem
type | A tevékenység típusa SetVariable | igen
érték | A megadott változó beállításához használt karakterlánc-literál vagy kifejezés objektum értéke | igen
variableName | A tevékenység által beállított változó neve | igen


## <a name="next-steps"></a>További lépések
A Data Factory által támogatott kapcsolódó vezérlési folyamatokkal kapcsolatos tevékenységek ismertetése: 

- [Változó tevékenységének hozzáfűzése](control-flow-append-variable-activity.md)
