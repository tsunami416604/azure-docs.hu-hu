---
title: SendGrid |} Microsoft Docs
description: "Az Azure App service Logic Apps alkalmazások létrehozása SendGrid Kapcsolatszolgáltatója lehetővé teszi, hogy küldjenek e-maileket, és kezelheti a címzettjeinek listája."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: bc4f1fc2-824c-4ed7-8de8-e82baff3b746
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 9ff0591741899d65b8274fb14ab3f3c8db9abe36
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-the-sendgrid-connector"></a>A SendGrid szolgáltatás összekötőjével az első lépései
SendGrid Kapcsolatszolgáltatója lehetővé teszi, hogy küldjenek e-maileket, és kezelheti a címzettjeinek listája.

Most hozzon létre egy Logic app kezdheti, lásd: [logikai alkalmazás létrehozása](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="create-a-connection-to-sendgrid"></a>Kapcsolatot létesíthet a SendGrid
A Logic apps sendgrid létrehozásához, létre kell hoznia egy **kapcsolat** adja meg a részleteket a következő tulajdonságokkal: 

| Tulajdonság | Szükséges | Leírás |
| --- | --- | --- |
| apiKey |Igen |Adja meg a SendGrid Api-kulcsát |

> [!INCLUDE [Steps to create a connection to SendGrid](../../includes/connectors-create-api-sendgrid.md)]
> 


Miután létrehozta a kapcsolatot, a műveletek végrehajtásához, és figyeljen az eseményindítók a használhatja.

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Bármely eseményindítók és a swagger definiált műveletek megtekintése, és semmilyen határnak a Lásd még: a [connector részleteket](/connectors/sendgrid/).

## <a name="more-connectors"></a>További összekötők
Lépjen vissza a [API-k lista](apis-list.md).