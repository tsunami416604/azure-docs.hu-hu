---
title: Az Azure CLI-Példaszkript – egy Azure Cache redis Get részletei |} A Microsoft Docs
description: Az Azure CLI-Példaszkript – egy Azure Cache redis Get részletei
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
tags: azure-service-management
ms.assetid: 155924e6-00d5-4a8c-ba99-5189f300464a
ms.service: cache
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: yegu
ms.openlocfilehash: 8b4c0311f935fa141170206f51e01b90eb4b98ae
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56234883"
---
# <a name="get-details-of-an-azure-cache-for-redis"></a>A Redis az Azure Cache részletekért

Ebben a forgatókönyvben elsajátíthatja az Azure Cache a Redis-példányt, beleértve a kiépítési állapot adatai olvashatók be.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Cache for Redis")]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja egy Azure Cache Redis-példány adatai olvashatók be. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis) | Kérje le az Azure Cache Redis-példány adatait. |


## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További Azure Cache a Redis CLI-példaszkripteket megtalálható a [Azure Cache Redis dokumentációt](../cli-samples.md).
