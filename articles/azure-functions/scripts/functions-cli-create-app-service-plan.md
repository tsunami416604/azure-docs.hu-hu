---
title: "Az Azure CLI-parancsfájlt minta - egy függvény-alkalmazás létrehozása az App Service-csomagot |} Microsoft Docs"
description: "Az Azure CLI-parancsfájlt minta - egy függvény-alkalmazás létrehozása az App Service-csomagot"
services: functions
documentationcenter: functions
author: syntaxc4
manager: cfowler
editor: 
tags: azure-service-management
ms.assetid: 0e221db6-ee2d-4e16-9bf6-a456cd05b6e7
ms.service: functions
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 04/11/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: c7868dda1e00882a944ac61d838c8b8987d5e740
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-function-app-in-an-app-service-plan"></a>Egy függvény-alkalmazás létrehozása az App Service-csomagot

Ez a parancsfájlpélda hoz létre egy Azure függvény alkalmazást, mert a függvények tárolója. A kijelölt App Service-csomag, ami azt jelenti, hogy a kiszolgáló erőforrásainak mindig szerepelnek. a függvény App létre.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure parancssori felületének 2.0-s vagy annál újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Mintaparancsfájl

Ezt a parancsfájlt hoz létre az Azure-függvény alkalmazást egy dedikált [App Service-csomag](../functions-scale.md#app-service-plan).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-app-service-plan/create-function-app-app-service-plan.sh "Create an Azure Function on an App Service plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Parancsfájl ismertetése

Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat. Ezt a parancsfájlt az alábbi parancsokat használja:

| Parancs | Megjegyzések |
|---|---|
| [az csoport létrehozása](https://docs.microsoft.com/cli/azure/group#az_group_create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az storage-fiók létrehozása](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_create) | Létrehoz egy Azure Storage-fiókot. |
| [az App Service-csomag létrehozása](https://docs.microsoft.com/cli/azure/appserviceplan#az_appserviceplan_create) | App Service-csomag létrehozása. |
| [az functionapp létrehozása](https://docs.microsoft.com/cli/azure/functionapp#az_functionapp_delete) | Létrehoz egy Azure függvény alkalmazást. |

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További Azure Functions CLI parancsfájl minták megtalálhatók a [dokumentáció az Azure Functions](../functions-cli-samples.md).
