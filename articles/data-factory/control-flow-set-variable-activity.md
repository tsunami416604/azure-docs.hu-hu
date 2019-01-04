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
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: douglasl
ms.openlocfilehash: cc573028779bcd6b77394bbeefbea58f714b835c
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54017344"
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
