---
title: Azure PowerShell – előfizetés erőforráscsoportra
description: Ez a cikk egy minta Azure PowerShell-parancsfájlt tartalmaz, amely bemutatja, hogyan fizethet elő egy erőforráscsoport Eseményrács-eseményeire, és hogyan szűrheti ki az erőforrást.
services: event-grid
documentationcenter: na
author: spelluru
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 5d41bfec5978d1baf38eaf1608f50375170d9929
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76720605"
---
# <a name="subscribe-to-events-for-a-resource-group-and-filter-for-a-resource-with-powershell"></a>Feliratkozás egy erőforráscsoport eseményeire és szűrés erőforrásra a PowerShell-lel

Ez a szkript létrehoz egy Event Grid-előfizetést egy erőforráscsoport eseményeihez. Szűrőt használ az erőforráscsoport adott erőforrásához tartozó események lekéréséhez.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script---stable"></a>Példaszkript – stabil

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/filter-events/filter-events.ps1 "Filter events")]

## <a name="sample-script---preview-module"></a>Mintaparancsfájl - előnézeti modul

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

Az előzetes mintaparancsfájlhoz az Event Grid modul szükséges. A telepítéshez futtassa a`Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

[!code-powershell[main](../../../powershell_scripts/event-grid/filter-events-preview/filter-events-preview.ps1 "Filter events")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsot használja az esemény-előfizetés létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Új-AzeventGridElőfizetés](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) | Event Grid-előfizetés létrehozása. |

## <a name="next-steps"></a>További lépések

* A felügyelt alkalmazásokra vonatkozó részleteket az [Azure felügyelt alkalmazásokat áttekintő](../overview.md) cikk ismerteti.
* A PowerShell-lel kapcsolatos további tudnivalókért tekintse meg az [Azure PowerShell dokumentációját](https://docs.microsoft.com/powershell/azure/get-started-azureps).
