---
title: 'CLI: ASP.NET Core alkalmazás létrehozása az ACR-ből'
description: Ismerje meg, hogyan automatizálható az App Service-alkalmazás üzembe helyezése és kezelése az Azure CLI használatával. Ez a példa bemutatja, hogyan hozhat létre egy linuxos ASP.NET Core alkalmazást az ACR-ből.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 3a2d1983-ff7b-476a-ac44-49ec2aabb31a
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/13/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 9733475753cb9d7fae5f452b33842602f56eff52
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74685969"
---
# <a name="create-an-aspnet-core-app-in-a-docker-container-in-app-service-from-azure-container-registry"></a>ASP.NET Core-alkalmazás létrehozása Docker-tárolóban a App Serviceból a Azure Container Registry

Ez a minta parancsfájl létrehoz egy erőforráscsoportot, egy Linux App Service tervet és egy alkalmazást. Ezután üzembe helyez ASP.NET Core-alkalmazást az Azure Container Registryben található Docker-tárolóval.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Az Azure CLI 2.0.52 vagy újabb verziójára lesz szüksége. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-linux-acr/deploy-linux-acr.sh "Linux Azure Container Registry")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja egy erőforráscsoport, App Service alkalmazás és az összes kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Létrehoz egy App Service-csomagot. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Létrehoz egy App Service alkalmazást. |
| [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) | Beállítja a App Service alkalmazás Docker-tárolóját. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További App Service CLI-példaszkripteket az [Azure App Service dokumentációjában](../samples-cli.md) találhat.
