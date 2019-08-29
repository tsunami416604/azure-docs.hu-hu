---
title: Változó tevékenység hozzáfűzése a Azure Data Factoryban | Microsoft Docs
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
ms.openlocfilehash: 24808c9ed4bda5e8d3b0ce23ab93bc59eb260374
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141725"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Változó tevékenység hozzáfűzése Azure Data Factory

A változó hozzáfűzése tevékenységgel hozzáadhat egy értéket egy Data Factory folyamatban definiált meglévő tömb változóhoz.

## <a name="type-properties"></a>Típus tulajdonságai

Tulajdonság | Leírás | Kötelező
-------- | ----------- | --------
name | A tevékenység neve a folyamatban | Igen
description | A tevékenység működését leíró szöveg | nem
type | A tevékenység típusa AppendVariable | igen
value | A megadott változóhoz való hozzáfűzéshez használt karakterlánc-literál vagy kifejezés objektum értéke | igen
variableName | Annak a változónak a neve, amely tevékenység alapján módosul, a változónak "Array" típusúnak kell lennie. | igen

## <a name="next-steps"></a>További lépések
A Data Factory által támogatott kapcsolódó vezérlési folyamatokkal kapcsolatos tevékenységek ismertetése: 

- [Változó tevékenység beállítása](control-flow-set-variable-activity.md)
