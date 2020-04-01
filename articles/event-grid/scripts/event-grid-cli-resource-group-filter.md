---
title: Azure CLI – előfizetés erőforráscsoportra & erőforrás szerinti szűrésre
description: Ez a cikk egy minta Azure CLI-parancsfájlt tartalmaz, amely bemutatja, hogyan fizethet elő egy erőforrás eseményrács-eseményeire, és hogyan szűrheti ki az erőforrást.
services: event-grid
documentationcenter: na
author: spelluru
ms.service: event-grid
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 3dfe31a38d1bc1ba8662246a5dec3f10d0d1c948
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76720826"
---
# <a name="subscribe-to-events-for-a-resource-group-and-filter-for-a-resource-with-azure-cli"></a>Feliratkozás egy erőforráscsoport eseményeire és szűrés erőforrásra az Azure CLI-vel

Ez a szkript létrehoz egy Event Grid-előfizetést egy erőforráscsoport eseményeihez. Szűrőt használ az erőforráscsoport adott erőforrásához tartozó események lekéréséhez.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Az előzetes verziójú példaszkripthez az Event Grid-bővítményre van szükség. A telepítéséhez futtassa az `az extension add --name eventgrid` parancsot.

## <a name="sample-script---stable"></a>Példaszkript – stabil

[!code-azurecli[main](../../../cli_scripts/event-grid/filter-events/filter-events.sh "Subscribe to Azure subscription")]

## <a name="sample-script---preview-extension"></a>Példaszkript – előzetes verziójú bővítmény

[!code-azurecli[main](../../../cli_scripts/event-grid/filter-events-preview/filter-events-preview.sh "Subscribe to Azure subscription")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsot használja az esemény-előfizetés létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az eventgrid event-subscription create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create) | Event Grid-előfizetés létrehozása. |
| [az eventgrid event-subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) – bővítmény verziója | Event Grid-előfizetés létrehozása. |

## <a name="next-steps"></a>További lépések

* Az előfizetések lekérdezéséről lásd: [Event Grid-előfizetések lekérdezése](../query-event-subscriptions.md).
* Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).
