---
title: "Egy Azure Cosmos DB-hez kapcsolódó Azure-függvény létrehozása | Microsoft Docs"
description: "Azure CLI-példaszkript – Egy Azure Cosmos DB-hez kapcsolódó Azure-függvény létrehozása"
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
ms.date: 01/22/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 2049de0adfd4be164cda69ca85782c528aeaf55c
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="create-an-azure-function-that-connects-to-an-azure-cosmos-db"></a>Egy Azure Cosmos DB-hez kapcsolódó Azure-függvény létrehozása

Ez az Azure Functions-példaszkript létrehoz egy függvényalkalmazást, amelyet hozzákapcsol egy Azure Cosmos DB-adatbázishoz. A létrehozott alkalmazásbeállítás, amely a kapcsolatot tartalmazza, egy [Cosmos DB-eseményindítóval vagy -kötéssel](..\functions-bindings-cosmosdb.md) használható.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a helyi CLI-t használja, akkor az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Példaszkript

Ez a példa létrehoz egy Azure-függvényalkalmazást, és hozzáadja a Cosmos DB végpontját és hozzáférési kulcsát egy alkalmazásbeállításhoz.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-cosmos-db/create-function-app-connect-to-cosmos-db.sh "Create an Azure Function that connects to an Azure Cosmos DB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja: A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az login](https://docs.microsoft.com/cli/azure/#az_login) | Bejelentkezik az Azure-ba. |
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot egy helyen |
| [az storage accounts create](https://docs.microsoft.com/cli/azure/storage/account) | Create a storage account |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az_functionapp_create) | Létrehoz egy új függvényalkalmazást. |
| [az cosmosdb create](https://docs.microsoft.com/cli/azure/cosmosdb#az_cosmosdb_create) | Létrehoz egy CosmosDB-adatbázist. |
| [az group delete](https://docs.microsoft.com/cli/azure/group#az_group_delete) | A fölöslegessé vált elemek eltávolítása |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure/overview).

További Azure Functions CLI-példaszkripteket az [Azure Functions dokumentációjában](../functions-cli-samples.md) találhat.




