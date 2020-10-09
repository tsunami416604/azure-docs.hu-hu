---
title: Azure cache létrehozása a Redis – Azure CLI
description: Ez az Azure CLI-mintakód azt mutatja be, hogyan lehet Azure cache-t létrehozni a Redis-példányhoz az az Redis Create paranccsal.
author: yegu-ms
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.author: yegu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7d6adcb0fb7b79dfffc413a1f89655eee14c70c6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87502851"
---
# <a name="create-an-azure-cache-for-redis"></a>Az Azure Cache for Redis létrehozása

Ebből a forgatókönyvből megtudhatja, hogyan hozhat létre Azure-gyorsítótárat a Redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-cache/create-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja egy erőforráscsoport és egy Azure-gyorsítótár létrehozásához a Redis. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az Redis Create](https://docs.microsoft.com/cli/azure/redis) | Azure cache létrehozása a Redis-példányhoz. |


## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További Azure cache a Redis CLI-szkriptekhez az [Azure cache Redis dokumentációjában](../cli-samples.md)található.
