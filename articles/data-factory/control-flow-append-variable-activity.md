---
title: Változótevékenység hozzáfűzése az Azure Data Factoryban
description: Megtudhatja, hogyan állíthatja be, hogy a Változó hozzáfűzése tevékenység értéket adjon hozzá egy Data Factory-folyamatban definiált meglévő tömbváltozóhoz
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
ms.openlocfilehash: bcecd3411b6e6e751032f33862a671d8adbca046
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73679975"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Változótevékenység hozzáfűzése az Azure Data Factoryban

A Változó hozzáfűzése tevékenység segítségével értéket adhat hozzá egy Data Factory-folyamatban definiált meglévő tömbváltozóhoz.

## <a name="type-properties"></a>Típus tulajdonságai

Tulajdonság | Leírás | Kötelező
-------- | ----------- | --------
név | A folyamatban lévő tevékenység neve | Igen
leírás | A tevékenység tevékenységét leíró szöveg | nem
type | A tevékenység típusa AppendVariable | igen
érték | Adott változóhoz való hozzáfűzéshez használt karakterlánc-konstans vagy kifejezésobjektum-érték | igen
változóNév | A tevékenység által módosítandó változó neve, a változónak "Tömb" típusúnak kell lennie. | igen

## <a name="next-steps"></a>További lépések
További információ a Data Factory által támogatott kapcsolódó vezérlési folyamattevékenységről: 

- [Változó tevékenység beállítása](control-flow-set-variable-activity.md)
