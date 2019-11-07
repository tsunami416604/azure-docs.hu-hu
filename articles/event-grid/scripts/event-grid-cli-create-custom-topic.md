---
title: Azure CLI-példaszkript – Egyéni témakör létrehozása | Microsoft Docs
description: Azure CLI-példaszkript – Egyéni témakör létrehozása
services: event-grid
documentationcenter: na
author: tfitzmac
ms.service: event-grid
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2018
ms.author: tomfitz
ms.openlocfilehash: 16c6586939a6dad248cef3abdabd78faf04a2381
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580379"
---
# <a name="create-event-grid-custom-topic-with-azure-cli"></a>Egyéni Event Grid-témakör létrehozása az Azure CLI-vel

Ez a szkript létrehoz egy egyéni Event Grid-témakört.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli[main](../../../cli_scripts/event-grid/create-custom-topic/create-custom-topic.sh "Create custom topic")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsot használja az egyéni témakör létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az eventgrid topic create](https://docs.microsoft.com/cli/azure/eventgrid/topic#az-eventgrid-topic-create) | Egyéni Event Grid-témakör létrehozása. |


## <a name="next-steps"></a>További lépések

* Az előfizetések lekérdezéséről lásd: [Event Grid-előfizetések lekérdezése](../query-event-subscriptions.md).
* Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).
