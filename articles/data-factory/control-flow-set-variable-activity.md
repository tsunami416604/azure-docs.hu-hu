---
title: Állítsa be a változó tevékenységhez az Azure Data Factoryban |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja a változó beállítása tevékenységhez meghatározott egy Data Factory-folyamatot egy létező változó értékét állíthatja be
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/10/2018
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: 71abfdff629f36b278488851b546c7371353a4d9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60767965"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Állítsa be a változó tevékenységhez az Azure Data Factoryban

A változó beállítása tevékenységhez használatával String, Bool, vagy egy Data Factory-folyamatot a megadott tömb típusú egy létező változó értékét állíthatja be.

## <a name="type-properties"></a>Tulajdonságok

Tulajdonság | Leírás | Szükséges
-------- | ----------- | --------
name | A folyamat a tevékenység neve | Igen
description | A tevékenység leírása leíró szöveg | nem
type | Tevékenység típus SetVariable | igen
value | Szövegkonstans vagy kifejezés objektum használt karakterlánc-érték a megadott változó beállítása | igen
variableName | Ez a tevékenység által beállított változó neve | igen


## <a name="next-steps"></a>További lépések
Ismerje meg a kapcsolódó vezérlésfolyam-tevékenységek a Data Factory által támogatott: 

- [Változó tevékenység hozzáfűzése](control-flow-append-variable-activity.md)
