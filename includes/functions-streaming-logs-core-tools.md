---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 1928a8238cd73087e3c199675574dd1395f4d76d
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881323"
---
#### <a name="built-in-log-streaming"></a>Beépített log streaming

Az Azure `logstream` -ban futó adott Function app-beli adatfolyam-naplók fogadásának megkezdéséhez használja a következő példát:

```bash
func azure functionapp logstream <FunctionAppName>
```

#### <a name="live-metrics-stream"></a>Élő metrikastream

A Function alkalmazáshoz tartozó [élő metrikastream](../articles/azure-monitor/app/live-stream.md) egy új böngészőablakban is megtekintheti a `--browser` lehetőséggel együtt, az alábbi példában látható módon:

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
