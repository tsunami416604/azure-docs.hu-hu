---
title: "Az Azure CLI-parancsfájlt minták – a kiszolgáló nélküli végrehajtáshoz függvény-alkalmazás létrehozása |} Microsoft Docs"
description: "Az Azure CLI-parancsfájlt minták – a kiszolgáló nélküli végrehajtáshoz függvény-alkalmazás létrehozása"
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
ms.date: 10/04/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 9436cafa775f2ad658c0c9901f7f8eae769c7052
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-function-app-for-serverless-execution"></a>A kiszolgáló nélküli végrehajtáshoz függvény-alkalmazás létrehozása

Ez a parancsfájlpélda hoz létre egy Azure függvény alkalmazást, mert a függvények tárolója. A függvény App létre kell hozni a [fogyasztás terv](../functions-scale.md#consumption-plan), ami ideális eseményvezérelt kiszolgáló nélküli munkaterheléseket.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha telepítése és a parancssori felület helyileg használata mellett dönt, ez a cikk szükséges az, hogy az Azure parancssori felület verzióját futtatja, 2.0-s vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Mintaparancsfájl

Ezt a parancsfájlt hoz létre egy Azure-függvény alkalmazás használata a [fogyasztás terv](../functions-scale.md#consumption-plan).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-consumption/create-function-app-consumption.sh "Create an Azure Function on a consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Parancsfájl ismertetése

Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat. Ezt a parancsfájlt az alábbi parancsokat használja:

| Parancs | Megjegyzések |
|---|---|
| [az csoport létrehozása](/cli/azure/group#az_group_create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az storage-fiók létrehozása](/cli/azure/storage/account#az_storage_account_create) | Létrehoz egy Azure Storage-fiókot. |
| [az functionapp létrehozása](/cli/azure/functionapp#az_functionapp_create) | Létrehoz egy függvény alkalmazást. |

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](/cli/azure/overview).

További Azure Functions CLI parancsfájl minták megtalálhatók a [dokumentáció az Azure Functions](../functions-cli-samples.md).
