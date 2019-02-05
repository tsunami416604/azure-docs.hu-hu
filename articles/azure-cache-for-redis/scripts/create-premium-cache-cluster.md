---
title: Azure CLI-Példaszkript – egy prémium szintű Azure Cache létrehozása redis-fürtözés |} A Microsoft Docs
description: Azure CLI-Példaszkript – egy prémium szintű Azure Cache létrehozása redis-fürtözés
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: 07bcceae-2521-4fe3-b88f-ed833104ddd2
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: 7ecae6711950ac180d8126060e9bc5a8e71e71d6
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55696098"
---
# <a name="create-a-premium-azure-cache-for-redis-with-clustering"></a>Hozzon létre egy prémium szintű Azure Cache redis klaszterezéssel

Ebben a forgatókönyvben, megtudhatja, hogyan hozhat létre egy 6 GB a prémium szintű Azure Cache a Redis-fürtözés engedélyezve van, és két szegmensek.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja egy erőforráscsoport létrehozásához és a egy prémium szintű Azure Cache redis-fürtözés engedélyezése. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az a redis létrehozása](https://docs.microsoft.com/cli/azure/redis) | Az Azure gyorsítótár létrehozása a Redis-példányt. |


## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További Azure Cache a Redis CLI-példaszkripteket megtalálható a [Azure Cache Redis dokumentációt](../cli-samples.md).
