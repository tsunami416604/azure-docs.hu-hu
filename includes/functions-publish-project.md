---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: fbb537c9584c948af37694b3bfc77a7c345e084d
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639113"
---
## <a name="deploy-the-function-app-project-to-azure"></a>A függvényalkalmazás projektjének üzembe helyezése az Azure-ban

Miután létrehozta a Function alkalmazást az Azure-ban, a [`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment) Core Tools paranccsal üzembe helyezheti a projekt kódját az Azure-ban. A következő parancsban cserélje le `<APP_NAME>` az elemet az alkalmazás nevére az előző lépésben.

```bash
func azure functionapp publish <APP_NAME>
```

A következőhöz hasonló kimenet jelenik meg, amelyet az olvashatóság érdekében csonkolt:

```output
Getting site publishing info...
...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
Functions in myfunctionapp:
    HttpTrigger - [httpTrigger]
        Invoke url: https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....
```

Másolja ki `Invoke url` az értékét `HttpTrigger`, amelyet mostantól használhat a függvény Azure-beli teszteléséhez. Az URL-cím `code` olyan lekérdezési karakterlánc-értéket tartalmaz, amely a függvény kulcsa. Ez a kulcs megnehezíti mások számára a HTTP-trigger végpont meghívását az Azure-ban.