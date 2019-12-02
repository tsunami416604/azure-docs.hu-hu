---
title: Azure PowerShell – Feliratkozás az Azure-előfizetésre
description: Azure PowerShell & Azure Event Grid parancsfájl-előfizetés az Azure-előfizetésre
services: event-grid
documentationcenter: na
author: spelluru
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/22/2019
ms.author: spelluru
ms.openlocfilehash: 8c90e39cef1420ec06b7f62ebf9982720ffebf94
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2019
ms.locfileid: "74664131"
---
# <a name="subscribe-to-events-for-an-azure-subscription-with-powershell"></a>Feliratkozás egy Azure-előfizetés eseményeire a PowerShell-lel

Ez a szkript létrehoz egy Event Grid-előfizetést egy Azure-előfizetés eseményeihez.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script---stable"></a>Példaszkript – stabil

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-azure-subscription/subscribe-to-azure-subscription.ps1 "Subscribe to Azure subscription")]

## <a name="sample-script---preview-module"></a>Minta parancsfájl – előzetes verziójú modul

Ehhez az előnézeti minta parancsfájlhoz a Event Grid modul szükséges. A telepítéséhez futtassa `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-azure-subscription-preview/subscribe-to-azure-subscription-preview.ps1 "Subscribe to Azure subscription")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsot használja az esemény-előfizetés létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Új – AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) | Event Grid-előfizetés létrehozása. |

## <a name="next-steps"></a>Következő lépések

* A felügyelt alkalmazásokra vonatkozó részleteket az [Azure felügyelt alkalmazásokat áttekintő](../overview.md) cikk ismerteti.
* A PowerShell-lel kapcsolatos további tudnivalókért tekintse meg az [Azure PowerShell dokumentációját](https://docs.microsoft.com/powershell/azure/get-started-azureps).
