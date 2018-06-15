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
ms.openlocfilehash: 70548db9ef98cc8fa59ebc11c1371325e63e02fc
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2018
ms.locfileid: "29348882"
---
A Cloud Shellben hozzon létre egy [webalkalmazást](../articles/app-service/app-service-web-overview.md) a `myAppServicePlan` App Service-csomagban. Ezt a [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) paranccsal teheti meg. A következő példában cserélje ki az *\<app_name>* nevet egy globálisan egyedi névre (érvényes karakterek: `a-z`, `0-9` és `-`). 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-local-git
```

A webalkalmazás létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg:

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

Ezzel létrehozott egy üres webalkalmazást, engedélyezett Git üzemelő példánnyal.

> [!NOTE]
> A távoli Git URL-címe a `deploymentLocalGitUrl` tulajdonságban látható, a következő formátumban: `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`. Mentse ezt az URL-t, mert később még szüksége lesz rá.
>

Tallózással keresse meg az újonnan létrehozott webalkalmazását.

```
http://<app_name>.azurewebsites.net
```

Az új webalkalmazásnak így kell kinéznie:
