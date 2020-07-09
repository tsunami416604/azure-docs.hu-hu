---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 1928a8238cd73087e3c199675574dd1395f4d76d
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "68881323"
---
#### <a name="built-in-log-streaming"></a>Beépített naplóstreamelés

A `logstream` paraméterrel elindíthatja az Azure-ban futó adott függvényalkalmazások streamnaplóinak fogadását, ahogyan az az alábbi példában látható:

```bash
func azure functionapp logstream <FunctionAppName>
```

#### <a name="live-metrics-stream"></a>Élő metrikastream

A `--browser` paraméter megadásával az [élő metrikastreamet](../articles/azure-monitor/app/live-stream.md) új böngészőablakban is megtekintheti, ahogyan az az alábbi példában látható:

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
