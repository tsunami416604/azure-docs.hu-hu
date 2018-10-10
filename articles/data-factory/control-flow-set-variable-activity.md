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
ms.date: 10/09/2018
ms.author: douglasl
ms.openlocfilehash: eed589cd78211cfe3955f591ce1f165e989b1308
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48904201"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Állítsa be a változó tevékenységhez az Azure Data Factoryban

A változó beállítása tevékenységhez használatával meghatározott egy Data Factory-folyamatot egy létező változó értékét állíthatja be.

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
