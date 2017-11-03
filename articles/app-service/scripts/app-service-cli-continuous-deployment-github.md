---
title: "Az Azure CLI-parancsfájlt minta - webalkalmazás létrehozása a folyamatos üzembe helyezés a Githubról |} Microsoft Docs"
description: "Az Azure CLI-parancsfájlt minta - webalkalmazás létrehozása a folyamatos üzembe helyezés a Githubról"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 0205c991-0989-4ca3-bb41-237dcc964460
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 06/19/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 2cb3380c633f0ac182ab23df1666f3b89654e65a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-web-app-with-continuous-deployment-from-github"></a>Webalkalmazás létrehozása a folyamatos üzembe helyezés a Githubról

Ez a parancsfájlpélda hoz létre egy webalkalmazást az App Service azok kapcsolódó erőforrásait, és beállítja egy GitHub-tárházban folyamatos üzembe helyezést. Folyamatos üzembe helyezés nélkül GitHub telepítését: [webalkalmazás létrehozása és központi telepítése a Githubból kód](app-service-cli-deploy-github.md). Ez a példa szüksége lesz:

* GitHub-tárházban alkalmazás kóddal, a rendszergazdai jogosultsággal kell rendelkeznie.
* A [személyes hozzáférési jogkivonat (PAT)](https://help.github.com/articles/creating-an-access-token-for-command-line-use) a GitHub-fiókjában.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure parancssori felületének 2.0-s vagy annál újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-github-continuous/deploy-github-continuous.sh?highlight=3-4 "Create a web app with continuous deployment from GitHub")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [az csoport létrehozása](https://docs.microsoft.com/cli/azure/group#az_group_create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az App Service-csomag létrehozása](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | App Service-csomag létrehozása. |
| [az alkalmazás-kulcs létrehozása](https://docs.microsoft.com/cli/azure/webapp#az_webapp_create) | Létrehoz egy Azure-webalkalmazásban. |
| [az webalkalmazás központi telepítési forrás konfigurációs](https://docs.microsoft.com/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config) | Azure-webalkalmazás társít egy Mercurial vagy a Git-tárházat. |
| [az alkalmazás kulcs Tallózás](https://docs.microsoft.com/cli/azure/webapp#az_webapp_browse) | Azure-webalkalmazás megnyitása a böngészőben. |

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További App Service CLI parancsfájl minták megtalálhatók a [Azure App Service-dokumentáció](../app-service-cli-samples.md).
