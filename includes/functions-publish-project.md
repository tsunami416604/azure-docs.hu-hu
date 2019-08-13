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
ms.openlocfilehash: 9fefba9c79e9c58956243de8db67ca4cf316251c
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68949993"
---
## <a name="deploy-the-function-app-project-to-azure"></a>A függvényalkalmazás projektjének üzembe helyezése az Azure-ban

Miután létrehozta a Function alkalmazást az Azure-ban, a [`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment) Core Tools paranccsal üzembe helyezheti a projekt kódját az Azure-ban. Ezekben a példákban cserélje `<APP_NAME>` le az alkalmazást az előző lépésből az alkalmazás nevére.

### <a name="c--javascript"></a>C\# /JavaScript

```command
func azure functionapp publish <APP_NAME>
```

### <a name="typescript"></a>TypeScript

```command
npm run build:production 
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