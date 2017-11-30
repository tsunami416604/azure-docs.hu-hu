---
title: "Egy Azure Storage kapcsolódó Azure-függvény létrehozása |} Microsoft Docs"
description: "Az Azure CLI-parancsfájlt minták –, amely kapcsolódik az Azure Storage Azure-függvény létrehozása"
services: functions
documentationcenter: functions
author: ggailey777
manager: cfowler
editor: 
tags: functions
ms.assetid: 
ms.service: functions
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 04/20/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 363a3fd1c80538495658720274840b921baa8675
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2017
---
# <a name="integrate-function-app-into-azure-storage-account"></a>Függvény alkalmazás integrálja az Azure Storage-fiók

Ez a parancsfájlpélda hoz létre, a függvény alkalmazás és a Storage-fiók.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha helyileg a CLI-t használ, győződjön meg arról, hogy az Azure parancssori felület verzióját futtatja, 2.0-s vagy újabb. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Mintaparancsfájl

Ez a minta egy Azure-függvény alkalmazást hoz létre, és hozzáadja a tárolási kapcsolati karakterlánc Alkalmazásbeállítás.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-storage/create-function-app-connect-to-storage-account.sh "Integrate Function App into Azure Storage Account")]


## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A parancsfájl-minta futtatása után futtassa a következő parancs futtatásával távolítsa el az erőforráscsoportot, és az összes kapcsolódó erőforrások:

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [az bejelentkezés](https://docs.microsoft.com/cli/azure/#login) | Jelentkezzen be az Azure-bA. |
| [az csoport létrehozása](https://docs.microsoft.com/cli/azure/group#az_group_create) | Hozzon létre egy erőforráscsoportot, amelynek a helye |
| [az storage-fiók létrehozása](https://docs.microsoft.com/cli/azure/storage/account) | Create a storage account |
| [az functionapp létrehozása](https://docs.microsoft.com/cli/azure/functionapp#az_functionapp_create) | Hozzon létre egy új funkció alkalmazást |
| [az csoport törlése](https://docs.microsoft.com/cli/azure/group#az_group_delete) | A fölöslegessé vált elemek eltávolítása |

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További Azure Functions CLI parancsfájl minták megtalálhatók a [dokumentáció az Azure Functions](../functions-cli-samples.md).
