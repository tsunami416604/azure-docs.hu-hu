---
title: 'CLI: ASP.NET Core alkalmazás létrehozása a Docker-ből'
description: Ismerje meg, hogyan automatizálható az App Service-alkalmazás üzembe helyezése és kezelése az Azure CLI használatával. Ez a példa bemutatja, hogyan hozhat létre ASP.NET Core alkalmazást a Docker hub-ból.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 3a2d1983-ff7b-476a-ac44-49ec2aabb31a
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: devx-track-dotnet, mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: dbb5751e27d02c0fc531a5527a19b1c6a96301f5
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94561436"
---
# <a name="create-an-aspnet-core-app-in-a-docker-container-from-docker-hub-using-azure-cli"></a>ASP.NET Core-alkalmazás létrehozása Docker-tárolóban a Docker hub-ból az Azure CLI használatával

Ez a minta parancsfájl létrehoz egy erőforráscsoportot, egy Linux App Service tervet és egy alkalmazást. Ezután üzembe helyez ASP.NET Core-alapú alkalmazást egy Docker-tároló használatával.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Az oktatóanyaghoz az Azure CLI 2,0-es vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-linux-docker/deploy-linux-docker.sh?highlight=6 "Linux Docker")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja egy erőforráscsoport, App Service alkalmazás és az összes kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Létrehoz egy App Service-csomagot. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Létrehoz egy App Service alkalmazást. |
| [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) | Beállítja a App Service alkalmazás Docker-tárolóját. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További App Service CLI-példaszkripteket az [Azure App Service dokumentációjában](../samples-cli.md) találhat.