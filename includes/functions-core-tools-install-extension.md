---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 04/06/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 4c9b579534d9a7f2c55e9c589b1738fe060b1cf2
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
Amikor helyileg funkciók fejlesztése a bővítmények segítségével az Azure Functions Core eszközök a Terminálszolgáltatások vagy a parancssorból is telepítheti. A következő `func extensions install` parancs telepíti az Azure Cosmos DB kötési bővítmény:

```
func extensions install --package Microsoft.Azure.WebJobs.Extensions.CosmosDB --version <target_version>
```

Cserélje le `<taget_version>` a csomag megadott verzióval. A következő egyes csomagot lapokon felsorolt érvényes verziók [NuGet.org](https://nuget.org).
