---
title: Állítsa be a változó tevékenységhez az Azure Data Factoryban |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja a változó beállítása tevékenységhez meghatározott egy Data Factory-folyamatot egy létező változó értékét állíthatja be
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
ms.date: 10/10/2018
ms.author: douglasl
ms.openlocfilehash: ff9bfce1f9262d78ba17abdd88c481d5057d5f38
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "49076419"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Állítsa be a változó tevékenységhez az Azure Data Factoryban

A változó beállítása tevékenységhez használatával String, Bool, vagy egy Data Factory-folyamatot a megadott tömb típusú egy létező változó értékét állíthatja be.

## <a name="type-properties"></a>Tulajdonságok

Tulajdonság | Leírás | Szükséges
-------- | ----------- | --------
név | A folyamat a tevékenység neve | Igen
leírás | A tevékenység leírása leíró szöveg | nem
type | Tevékenység típus SetVariable | igen
érték | Szövegkonstans vagy kifejezés objektum használt karakterlánc-érték a megadott változó beállítása | igen
variableName | Ez a tevékenység által beállított változó neve | igen


## <a name="next-steps"></a>További lépések
Ismerje meg a kapcsolódó vezérlésfolyam-tevékenységek a Data Factory által támogatott: 

- [Változó tevékenység hozzáfűzése](control-flow-append-variable-activity.md)
