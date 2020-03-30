---
title: Változótevékenység beállítása az Azure Data Factoryban
description: Ismerje meg, hogyan állítható be a Változó beállítása tevékenység egy Data Factory-folyamatban definiált meglévő változó értékének beállítása
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930645"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Változótevékenység beállítása az Azure Data Factoryban

A Változó beállítása tevékenység segítségével állítsa be a Data Factory-folyamatban definiált Karakterlánc, Bool vagy Tömb típusú meglévő változó értékét.

## <a name="type-properties"></a>Típus tulajdonságai

Tulajdonság | Leírás | Kötelező
-------- | ----------- | --------
név | A folyamatban lévő tevékenység neve | Igen
leírás | A tevékenység tevékenységét leíró szöveg | nem
type | A tevékenység típusa SetVariable | igen
érték | A megadott változó beállításához használt karakterlánc-konstans vagy kifejezésobjektum-érték | igen
változóNév | A tevékenység által beállítandó változó neve | igen


## <a name="next-steps"></a>További lépések
További információ a Data Factory által támogatott kapcsolódó vezérlési folyamattevékenységről: 

- [Változó tevékenység hozzáfűzése](control-flow-append-variable-activity.md)
