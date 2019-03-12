---
title: Az Azure Data Factory változó tevékenység hozzáfűzése |} A Microsoft Docs
description: Ismerje meg, hogyan állíthatja be a változó hozzáfűzése tevékenység tömbváltozóhoz egy meglévő Data Factory-folyamatok meghatározott érték hozzáadása
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/09/2018
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: a5efe946000eb00e65d314ae53d7136761e2109d
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57575154"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Az Azure Data Factory változó tevékenység hozzáfűzése

A változó hozzáfűzése tevékenység használatával adjon meg egy értéket egy meglévő, egy Data Factory-folyamatot meghatározott tömbváltozóhoz.

## <a name="type-properties"></a>Tulajdonságok

Tulajdonság | Leírás | Szükséges
-------- | ----------- | --------
név | A folyamat a tevékenység neve | Igen
leírás | A tevékenység leírása leíró szöveg | nem
type | Tevékenység típus AppendVariable | igen
érték | Karakterlánc változóban megadott összefűzésére használható szövegkonstans vagy kifejezés objektum értéke | igen
variableName | A tevékenység, a változó által módosító változó nevének kell lennie a "Array" típus | igen

## <a name="next-steps"></a>További lépések
Ismerje meg a kapcsolódó vezérlésfolyam-tevékenységek a Data Factory által támogatott: 

- [Változó beállítása tevékenységhez](control-flow-set-variable-activity.md)
