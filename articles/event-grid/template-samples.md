---
title: Azure Resource Manager-sablonminták – Event Grid | Microsoft Docs
description: Azure Resource Manager-sablonminták az Event Gridhez
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.date: 01/04/2019
ms.author: tomfitz
ms.openlocfilehash: 788d23c7bddd90c1e12a118742c651eb9759529c
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2019
ms.locfileid: "54062961"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Azure Resource Manager-sablonok az Event Gridhez

A JSON-szintaxist és a egy sablonban használandó tulajdonságokat: [Microsoft.EventGrid erőforrástípusok](/azure/templates/microsoft.eventgrid/allversions). A következő táblázat az Event Gridhez való Azure Resource Manager-sablonokra mutató hivatkozásokat tartalmaz.

| | |
|-|-|
|**Event Grid-feliratkozások**||
| [Egyéni témakör és feliratkozás WebHook-végponttal](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid)| Üzembe helyez egy egyéni Event Grid-témakört, és létrehoz egy Webhook-végpontot használó feliratkozást az üzembe helyezett témakörhöz. |
| [Egyéni témakör és feliratkozás EventHub-végponttal](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Létrehoz egy Event Grid-feliratkozást egy egyéni témakörhöz. A feliratkozás egy eseményközpontot használ végpontként. |
| [Azure-előfizetés vagy erőforráscsoport-előfizetés](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook)| Feliratkozik egy erőforráscsoport vagy Azure-előfizetés eseményeire. Az üzembe helyezés során célként megadott erőforráscsoport az események forrása. A feliratkozás egy webhookot használ végpontként. |
| [Blob Storage-fiók és feliratkozás](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage)| Üzembe helyez egy Azure Blob Storage-fiókot, és feliratkozik a vele kapcsolatos eseményekre. |
| | |
