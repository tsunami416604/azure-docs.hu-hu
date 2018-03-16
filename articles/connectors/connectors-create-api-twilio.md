---
title: "Adja hozzá a Twilio-összekötő az Azure Logic apps |} Microsoft Docs"
description: "A REST API-paraméterekkel rendelkező Twilio-összekötő áttekintése"
services: logic-apps
documentationcenter: 
author: ecfan
manager: anneta
editor: 
tags: connectors
ms.assetid: 43116187-4a2f-42e5-9852-a0d62f08c5fc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/19/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 2bb7961c850fc8a35f3e114d9497941b188cf51b
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-twilio-connector"></a>A Twilio-összekötő az első lépései
A globális SMS, MMS és IP-üzeneteket küldjön és fogadjon Twilio csatlakozni. A Twilio a következőket teheti:

* Az üzleti folyamat Twilio származó adatok alapján történő létrehozása. 
* Egy üzenet, a lista üzenetek és a további műveleteket használni. Ezeket a műveleteket válaszol, és végezze el a kimeneti más műveletek érhető el. Például egy új Twilio-üzenetet kap, ha akkor igénybe ezt az üzenetet, és a Service Bus munkafolyamat használatával. 

Hozzon létre egy logic app; első lépései Lásd: [logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-twilio"></a>Kapcsolatot létesíthet Twilio
Ezt az összekötőt a logic apps hozzáadásakor adja meg a következő Twilio-értékeket:

| Tulajdonság | Szükséges | Leírás |
| --- | --- | --- |
| Fiókazonosító |Igen |Írja be a Twilio-fiók Azonosítóját |
| Hozzáférési jogkivonat |Igen |Adja meg a Twilio-hozzáférési jogkivonat |

> [!INCLUDE [Steps to create a connection to Twilio](../../includes/connectors-create-api-twilio.md)]
> 
> 

Ha a Twilio-hozzáférési jogkivonat nem rendelkezik, tekintse meg a [felhasználói identitás- & hozzáférési jogkivonatok](https://www.twilio.com/docs/api/chat/guides/identity).

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Bármely eseményindítók és a swagger definiált műveletek megtekintése, és semmilyen határnak a Lásd még: a [connector részleteket](/connectors/twilio/).

## <a name="more-connectors"></a>További összekötők
Lépjen vissza a [API-k lista](apis-list.md).