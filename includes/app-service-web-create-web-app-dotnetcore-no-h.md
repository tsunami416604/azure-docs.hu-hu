---
title: fájl belefoglalása
description: fájl belefoglalása
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 727b82692400da166d892f2385e01b0ec19863e2
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
Hozzon létre egy [webalkalmazást](../articles/app-service/containers/app-service-linux-intro.md) az `myAppServicePlan` App Service-csomagban. 

A Cloud Shellben használhatja az [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) parancsot. A következő példában cserélje ki az `<app_name>` nevet egy globálisan egyedi névre (érvényes karakterek: `a-z`, `0-9` és `-`). A futtatókörnyezet beállítása `dotnetcore|1.1` lett. Az összes támogatott futtatókörnyezet megtekintéséhez futtassa az [`az webapp list-runtimes`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_list_runtimes) parancsot. 

```azurecli-interactive
# Bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "dotnetcore|1.1" --deployment-local-git
# Powershell
az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "dotnetcore|1.1" --deployment-local-git
```

A webalkalmazás létrehozása után az Azure CLI az alábbi példához hasonló eredményeket jelenít meg:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Ezzel létrehozott egy üres webalkalmazást egy Linux-tárolóban, engedélyezett git üzemelő példánnyal.

> [!NOTE]
> A távoli Git URL-címe a `deploymentLocalGitUrl` tulajdonságban látható, a következő formátumban: `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`. Mentse ezt az URL-t, mert később még szüksége lesz rá.
>
