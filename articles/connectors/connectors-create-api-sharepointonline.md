---
title: A SharePoint Online-összekötő használata logic Apps |} A Microsoft Docs
description: Hozzon létre a logic apps a SharePoint Online-összekötő mange listák a Sharepointban.
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: e0ec3149-507a-409d-8e7b-d5fbded006ce
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/19/2016
ms.author: estfan; ladocs
ms.openlocfilehash: be104080e1056fa2181e54919d014154d16a60a4
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38237236"
---
# <a name="get-started-with-the-sharepoint-online-connector"></a>A SharePoint Online-összekötő használatának első lépései
A SharePoint Online-összekötő segítségével kezelheti a SharePoint-listák.  

Használandó [összekötőket](apis-list.md), először hozzon létre egy logikai alkalmazást. Úgy kezdheti [egy logikai alkalmazás most már létrehozásának](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-sharepoint-online"></a>Csatlakozhat a SharePoint online-hoz
A logikai alkalmazás bármely szolgáltatási férhet hozzá, akkor először kell hoznia egy *kapcsolat* a szolgáltatáshoz. A [kapcsolat](connectors-overview.md) kapcsolatot biztosít a logikai alkalmazás és a egy másik szolgáltatás között.  

### <a name="create-a-connection-to-sharepoint-online"></a>Hozzon létre egy kapcsolatot a SharePoint online-hoz
> [!INCLUDE [Steps to create a connection to SharePoint](../../includes/connectors-create-api-sharepointonline.md)]


## <a name="use-a-sharepoint-online-trigger"></a>A SharePoint Online triggert
Egy trigger egy eseményt, amely a logikai alkalmazásban definiált munkafolyamat elindításához használható. [További tudnivalók a triggerek](../logic-apps/logic-apps-overview.md#logic-app-concepts).  

> [!INCLUDE [Steps to create a SharePoint Online trigger](../../includes/connectors-create-api-sharepointonline-trigger.md)]


## <a name="use-a-sharepoint-online-action"></a>A SharePoint Online művelet használata
Művelet definiálva, a logikai alkalmazás a munkafolyamat által végzett művelet. [További információért azokról a műveletekről](../logic-apps/logic-apps-overview.md#logic-app-concepts).  

> [!INCLUDE [Steps to create a SharePoint Online action](../../includes/connectors-create-api-sharepointonline-action.md)]


## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Megtekintheti a valamennyi eseményindítót és műveletet a swaggerben meghatározott, és emellett a korlátozott a [összekötő részletei](/connectors/sharepoint/).

## <a name="next-steps"></a>További lépések
[Logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

