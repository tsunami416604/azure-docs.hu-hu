---
title: Állomásnév, portok, kulcsok – Azure cache a Redis-hez – Azure CLI
description: Ez az Azure CLI-mintakód azt mutatja be, hogyan kérhető le egy Azure cache-hez tartozó állomásnév, portok és kulcsok a Redis-példányhoz.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: 199c5707e9ecd887af64e271184e151ce0966745
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "75411300"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-cache-for-redis"></a>Az Azure cache gazdagép-, port-és kulcsainak beolvasása a Redis

Ebből a forgatókönyvből megtudhatja, hogyan kérheti le a Redis-példány Azure cache-hez való csatlakozáshoz használt állomásnév, portok és kulcsok beolvasását.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Cache for Redis")]


## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja egy Azure cache Redis-példányhoz tartozó állomásnév, kulcsok és portok lekéréséhez. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az Redis show](https://docs.microsoft.com/cli/azure/redis) | A Redis-példányhoz tartozó Azure cache részleteinek beolvasása. |
| [az Redis List-Keys](https://docs.microsoft.com/cli/azure/redis) | Redis-példányhoz tartozó Azure cache elérési kulcsainak beolvasása. |


## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További Azure cache a Redis CLI-szkriptekhez az [Azure cache Redis dokumentációjában](../cli-samples.md)található.
