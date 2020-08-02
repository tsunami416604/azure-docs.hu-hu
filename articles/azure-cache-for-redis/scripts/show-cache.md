---
title: A Redis készült Azure cache részleteinek beolvasása – Azure CLI
description: Ez az Azure CLI-mintakód azt mutatja be, hogyan kérhető le a Redis-példányhoz tartozó Azure cache részletei, beleértve annak kiépítési állapotát is.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8874a17b40b60a194811630b2f93d2be9d7faf7f
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87494706"
---
# <a name="get-details-of-an-azure-cache-for-redis"></a>A Redis készült Azure cache részleteinek beolvasása

Ebből a forgatókönyvből megtudhatja, hogyan kérhet le egy Azure cache Redis-példány részleteit, beleértve annak kiépítési állapotát is.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Cache for Redis")]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja a Redis-példányhoz tartozó Azure cache részleteinek lekéréséhez. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az Redis show](https://docs.microsoft.com/cli/azure/redis) | A Redis-példányhoz tartozó Azure cache részleteinek beolvasása. |


## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További Azure cache a Redis CLI-szkriptekhez az [Azure cache Redis dokumentációjában](../cli-samples.md)található.
