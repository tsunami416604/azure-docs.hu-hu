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
ms.date: 03/27/2018
ms.author: tomfitz
ms.openlocfilehash: f4d6a663b4e0d2c2166028051e713668534b20bc
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
ms.locfileid: "30246270"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Azure Resource Manager-sablonok az Event Gridhez

A következő táblázat az Event Gridhez való Azure Resource Manager-sablonokra mutató hivatkozásokat tartalmaz.

| | |
|-|-|
|**Event Grid-feliratkozások**||
| [Egyéni témakör és feliratkozás WebHook-végponttal](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid)| Üzembe helyez egy egyéni Event Grid-témakört, és létrehoz egy Webhook-végpontot használó feliratkozást az üzembe helyezett témakörhöz. |
| [Egyéni témakör és feliratkozás EventHub-végponttal](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/subscribeCustomTopicToEventHub.json)| Létrehoz egy Event Grid-feliratkozást egy már létező egyéni témakörhöz. A feliratkozás egy eseményközpontot használ végpontként. |
| [Erőforráscsoport-feliratkozás](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/subscribeResourceGroupToWebHook.json)| Feliratkozik egy erőforráscsoport eseményeire. Az üzembe helyezés során célként megadott erőforráscsoport az események forrása. A feliratkozás egy eseményközpontot használ végpontként. |
| [Blob Storage-fiók és feliratkozás](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/createBlobAndSubscribe.json)| Üzembe helyez egy Azure Blob Storage-fiókot, és feliratkozik a vele kapcsolatos eseményekre. |
| | |
