---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/27/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 1b553cbd720fcb76899844712ce5053af46f7ccb
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47452955"
---
## <a name="deploy-the-function-app-project-to-azure"></a>A függvényalkalmazás projektjének üzembe helyezése az Azure-ban

Miután létrehozta a függvényalkalmazást az Azure-ban, a(z) [`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment) paranccsal üzembe helyezheti a projektkódot az Azure-ban.

```bash
func azure functionapp publish <FunctionAppName>
```

A következőhöz hasonló eredményt kell látnia, amelyet lerövidítettünk a könnyebb olvashatóság érdekében.

```output
Getting site publishing info...

...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
```

Mostantól tesztelheti a függvényeit az Azure-ban.