---
title: Azure PowerShell-példaszkript – Feliratkozás egyéni témakörre | Microsoft Docs
description: Ez a cikk egy minta Azure PowerShell parancsfájlt tartalmaz, amely bemutatja, hogyan fizethet elő Event Grid eseményekre egy egyéni témakörben.
ms.devlang: powershell
ms.topic: sample
ms.date: 07/08/2020
ms.openlocfilehash: f37a90b84e5e55298efc0b3cd53812db95e4d86d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87460728"
---
# <a name="subscribe-to-events-for-a-custom-topic-with-powershell"></a>Feliratkozás egy egyéni témakör eseményeire a PowerShell-lel

Ez a szkript létrehoz egy Event Grid-előfizetést egy egyéni témakör eseményeihez.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Az előnézeti minta parancsfájlhoz a Event Grid modul szükséges. A telepítéséhez futtassa a következőt `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

## <a name="sample-script---stable"></a>Példaszkript – stabil

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-custom-topic/subscribe-to-custom-topic.ps1 "Subscribe to custom topic")]

## <a name="sample-script---preview-module"></a>Minta parancsfájl – előzetes verziójú modul

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-custom-topic-preview/subscribe-to-custom-topic-preview.ps1 "Subscribe to custom topic")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsot használja az esemény-előfizetés létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [Új – AzEventGridSubscription](/powershell/module/az.eventgrid/new-azeventgridsubscription) | Event Grid-előfizetés létrehozása. |

## <a name="next-steps"></a>További lépések

* A felügyelt alkalmazásokra vonatkozó részleteket az [Azure felügyelt alkalmazásokat áttekintő](../overview.md) cikk ismerteti.
* A PowerShell-lel kapcsolatos további tudnivalókért tekintse meg az [Azure PowerShell dokumentációját](/powershell/azure/get-started-azureps).
