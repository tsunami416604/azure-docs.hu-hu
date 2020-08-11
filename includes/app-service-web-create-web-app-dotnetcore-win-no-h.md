---
title: fájlbefoglalás
description: fájlbefoglalás
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 3060e12b294531a697c1486b3a020dc1da1484c4
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88077676"
---
Hozzon létre egy [webalkalmazást](../articles/app-service/overview.md#app-service-on-linux) a `myAppServicePlan` app Service tervben. 

A Cloud Shell a [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) parancsot használhatja. A következő példában cserélje ki az `<app-name>` nevet egy globálisan egyedi névre (érvényes karakterek: `a-z`, `0-9` és `-`). 

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --deployment-local-git
```

A webalkalmazás létrehozása után az Azure CLI az alábbi példához hasonló eredményeket jelenít meg:

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "clientCertExclusionPaths": null,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app-name&gt;.azurewebsites.net",
  "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

> [!NOTE]
> A távoli Git URL-címe a `deploymentLocalGitUrl` tulajdonságban látható, a következő formátumban: `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Mentse ezt az URL-t, mert később még szüksége lesz rá.
>
