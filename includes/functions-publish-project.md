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
ms.openlocfilehash: 48bb91b3b2e9a31de63e515edb857bc2a170ea79
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178904"
---
## <a name="deploy-the-function-app-project-to-azure"></a>A függvényalkalmazás projektjének üzembe helyezése az Azure-ban

Miután a függvényalkalmazás létrehozása az Azure-ban, használhatja a [ `func azure functionapp publish` ](../articles/azure-functions/functions-run-local.md#project-file-deployment) Core Tools parancsot, hogy üzembe helyezze projektkódját az Azure-bA. A következő parancsban cserélje le a `<APP_NAME>` az előző lépésben az alkalmazás nevére.

```bash
func azure functionapp publish <APP_NAME>
```

A következő, az olvashatóság érdekében csonkolva lett hasonló kimenetet fog látni.

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

Másolja a meghívása URL-címet a HttpTrigger, amely mostantól használhatja az Azure-ban a függvény teszteléséhez. Az URL-címet tartalmaz egy `code` lekérdezési karakterlánc értékét, amely az a billentyűt. Ez a kulcs megnehezíti mások számára a HTTP-eseményindító végpont meghívása az Azure-ban.