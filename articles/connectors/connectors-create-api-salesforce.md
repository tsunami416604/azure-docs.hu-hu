---
title: Bemutatják, hogyan használhatja a Salesforce-összekötőt a logic Apps alkalmazásait |} Microsoft Docs
description: Az Azure App service logic Apps alkalmazások létrehozása A Salesforce-összekötő API-jával különféle műveleteket végezhet a Salesforce-objektumokon.
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: 54fe5af8-7d2a-4da8-94e7-15d029e029bf
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/05/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 4278837bb5653b66223374aa728bdc81b279fff7
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295795"
---
# <a name="get-started-with-the-salesforce-connector"></a>A Salesforce-összekötő az első lépései
A Salesforce-összekötő API-jával különféle műveleteket végezhet a Salesforce-objektumokon.

Használandó [a csatlakozókat](apis-list.md), először hozzon létre egy logikai alkalmazást. Elkezdheti által [logikai alkalmazás létrehozása most](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-salesforce-connector"></a>Kapcsolódás a Salesforce-összekötő
A Logic Apps alkalmazást bármely szolgáltatás hozzáférni, először hozzon létre egy *kapcsolat* a szolgáltatáshoz. A [kapcsolat](connectors-overview.md) biztosít a logikai alkalmazás és egy másik szolgáltatás közötti kapcsolat.  

### <a name="create-a-connection-to-salesforce-connector"></a>Kapcsolatot létesíthet a Salesforce-összekötő
> [!INCLUDE [Steps to create a connection to Salesforce Connector](../../includes/connectors-create-api-salesforce.md)]
> 
> 

## <a name="use-a-salesforce-connector-trigger"></a>Salesforce összekötő eseményindítók
Egy eseményindító nem egy eseményt, a logikai alkalmazás definiált munkafolyamat indításához használható. [További tudnivalók az eseményindítók](../logic-apps/logic-apps-overview.md#logic-app-concepts).

> [!INCLUDE [Steps to create a Salesforce trigger](../../includes/connectors-create-api-salesforce-trigger.md)]
> 
> 

## <a name="add-a-condition"></a>Feltétel hozzáadása
> [!INCLUDE [Steps to create a Salesforce condition](../../includes/connectors-create-api-salesforce-condition.md)]
> 
> 

## <a name="use-a-salesforce-connector-action"></a>A Salesforce-összekötő művelettel
Egy művelet során a logikai alkalmazás definiált munkafolyamat által végzett. [További információ a műveletek](../logic-apps/logic-apps-overview.md#logic-app-concepts).

> [!INCLUDE [Steps to create a Salesforce action](../../includes/connectors-create-api-salesforce-action.md)]
> 
> 

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Bármely eseményindítók és a swagger definiált műveletek megtekintése, és semmilyen határnak a Lásd még: a [connector részleteket](/connectors/salesforce/). 

## <a name="next-steps"></a>További lépések
[Logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

