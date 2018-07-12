---
title: A Salesforce-összekötő használata a logic Apps |} A Microsoft Docs
description: Logikai alkalmazások létrehozása az Azure App service használatával. A Salesforce-összekötő API-jával különféle műveleteket végezhet a Salesforce-objektumokon.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38237301"
---
# <a name="get-started-with-the-salesforce-connector"></a>A Salesforce-összekötő használatának első lépései
A Salesforce-összekötő API-jával különféle műveleteket végezhet a Salesforce-objektumokon.

Használandó [összekötőket](apis-list.md), először hozzon létre egy logikai alkalmazást. Úgy kezdheti [egy logikai alkalmazás most már létrehozásának](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-salesforce-connector"></a>Csatlakozhat Salesforce-összekötő
A logikai alkalmazás bármely szolgáltatási férhet hozzá, akkor először kell hoznia egy *kapcsolat* a szolgáltatáshoz. A [kapcsolat](connectors-overview.md) kapcsolatot biztosít a logikai alkalmazás és a egy másik szolgáltatás között.  

### <a name="create-a-connection-to-salesforce-connector"></a>Kapcsolatot létesíthet a Salesforce-összekötő
> [!INCLUDE [Steps to create a connection to Salesforce Connector](../../includes/connectors-create-api-salesforce.md)]
> 
> 

## <a name="use-a-salesforce-connector-trigger"></a>Salesforce-összekötő triggert
Egy trigger egy eseményt, amely a logikai alkalmazásban definiált munkafolyamat elindításához használható. [További tudnivalók a triggerek](../logic-apps/logic-apps-overview.md#logic-app-concepts).

> [!INCLUDE [Steps to create a Salesforce trigger](../../includes/connectors-create-api-salesforce-trigger.md)]
> 
> 

## <a name="add-a-condition"></a>Feltétel hozzáadása
> [!INCLUDE [Steps to create a Salesforce condition](../../includes/connectors-create-api-salesforce-condition.md)]
> 
> 

## <a name="use-a-salesforce-connector-action"></a>A Salesforce-összekötő művelet használata
Művelet definiálva, a logikai alkalmazás a munkafolyamat által végzett művelet. [További információért azokról a műveletekről](../logic-apps/logic-apps-overview.md#logic-app-concepts).

> [!INCLUDE [Steps to create a Salesforce action](../../includes/connectors-create-api-salesforce-action.md)]
> 
> 

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Megtekintheti a valamennyi eseményindítót és műveletet a swaggerben meghatározott, és emellett a korlátozott a [összekötő részletei](/connectors/salesforce/). 

## <a name="next-steps"></a>További lépések
[Logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

