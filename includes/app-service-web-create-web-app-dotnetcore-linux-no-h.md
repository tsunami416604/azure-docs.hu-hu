---
title: fájl belefoglalása
description: fájl belefoglalása
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 08/06/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 2dc35727a82459750b4414b6e3eeddf28b31b718
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68824725"
---
Hozzon létre egy [webalkalmazást](../articles/app-service/containers/app-service-linux-intro.md) az `myAppServicePlan` App Service-csomagban. 

A Cloud Shellben használhatja az [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest) parancsot. A következő példában cserélje ki az `<app-name>` nevet egy globálisan egyedi névre (érvényes karakterek: `a-z`, `0-9` és `-`). A futtatókörnyezet beállítása `DOTNETCORE|2.2` lett. Az összes támogatott futtatókörnyezet megtekintéséhez futtassa az [`az webapp list-runtimes --linux`](/cli/azure/webapp?view=azure-cli-latest) parancsot. 

```azurecli-interactive
# Bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "DOTNETCORE|2.2" --deployment-local-git
# PowerShell
az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "DOTNETCORE|2.2" --deployment-local-git
```

A webalkalmazás létrehozása után az Azure CLI az alábbi példához hasonló eredményeket jelenít meg:

```json
Local git is configured with url of 'https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Ezzel létrehozott egy üres webalkalmazást egy Linux-tárolóban, engedélyezett git üzemelő példánnyal.

> [!NOTE]
> A távoli Git URL-címe a `deploymentLocalGitUrl` tulajdonságban látható, a következő formátumban: `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Mentse ezt az URL-t, mert később még szüksége lesz rá.
>
