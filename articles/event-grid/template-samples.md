---
title: Azure Resource Manager-sablonminták – Event Grid | Microsoft Docs
description: Ez a cikk a GitHub-Azure Event Grid Azure Resource Manager sablonjának mintáit sorolja fel.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 38d8db0bcc504760595fe51b63072f63e785577a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720622"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Azure Resource Manager-sablonok az Event Gridhez

A sablonban használandó JSON-szintaxis és-tulajdonságok megtekintéséhez lásd: [Microsoft. EventGrid-erőforrástípusok](/azure/templates/microsoft.eventgrid/allversions). A következő táblázat az Event Gridhez való Azure Resource Manager-sablonokra mutató hivatkozásokat tartalmaz.

| | |
|-|-|
|**Event Grid-feliratkozások**||
| [Egyéni témakör és feliratkozás WebHook-végponttal](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid)| Üzembe helyez egy egyéni Event Grid-témakört, és létrehoz egy Webhook-végpontot használó feliratkozást az üzembe helyezett témakörhöz. |
| [Egyéni témakör és feliratkozás EventHub-végponttal](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Létrehoz egy Event Grid-feliratkozást egy egyéni témakörhöz. A feliratkozás egy eseményközpontot használ végpontként. |
| [Azure-előfizetés vagy erőforráscsoport-előfizetés](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook)| Feliratkozik egy erőforráscsoport vagy Azure-előfizetés eseményeire. Az üzembe helyezés során célként megadott erőforráscsoport az események forrása. A feliratkozás egy webhookot használ végpontként. |
| [Blob Storage-fiók és feliratkozás](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage)| Üzembe helyez egy Azure Blob Storage-fiókot, és feliratkozik a vele kapcsolatos eseményekre. |
| | |
