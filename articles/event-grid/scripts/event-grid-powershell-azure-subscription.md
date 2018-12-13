---
title: Azure PowerShell-példaszkript – Feliratkozás Azure-előfizetésre | Microsoft Docs
description: Azure PowerShell-példaszkript – Feliratkozás Azure-előfizetésre
services: event-grid
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/10/2018
ms.author: tomfitz
ms.openlocfilehash: 7d4b7f49edca6c72681734f70dba3be8722edd91
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53269311"
---
# <a name="subscribe-to-events-for-an-azure-subscription-with-powershell"></a>Feliratkozás egy Azure-előfizetés eseményeire a PowerShell-lel

Ez a szkript létrehoz egy Event Grid-előfizetést egy Azure-előfizetés eseményeihez.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Az előzetes verzió minta a szkripthez az Event Grid-modul. Telepítéséhez futtassa `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

## <a name="sample-script---stable"></a>Példaszkript – stabil

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-azure-subscription/subscribe-to-azure-subscription.ps1 "Subscribe to Azure subscription")]

## <a name="sample-script---preview-module"></a>A példaszkript – előzetes verzió modul

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-azure-subscription-preview/subscribe-to-azure-subscription-preview.ps1 "Subscribe to Azure subscription")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsot használja az esemény-előfizetés létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzureRmEventGridSubscription](https://docs.microsoft.com/powershell/module/azurerm.eventgrid/new-azurermeventgridsubscription) | Event Grid-előfizetés létrehozása. |

## <a name="next-steps"></a>További lépések

* A felügyelt alkalmazásokra vonatkozó részleteket az [Azure felügyelt alkalmazásokat áttekintő](../overview.md) cikk ismerteti.
* A PowerShell-lel kapcsolatos további tudnivalókért tekintse meg az [Azure PowerShell dokumentációját](https://docs.microsoft.com/powershell/azure/get-started-azureps).
