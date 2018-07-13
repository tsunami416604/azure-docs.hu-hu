---
title: Adja hozzá a Twilio-összekötőt az Azure Logic Apps |} A Microsoft Docs
description: A Twilio-összekötőt a REST API-paraméterekkel áttekintése
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: 43116187-4a2f-42e5-9852-a0d62f08c5fc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/19/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 8bcf69a7c8e04cb45d795fd0d6f20d477c15865d
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38652005"
---
# <a name="get-started-with-the-twilio-connector"></a>A Twilio-összekötő használatának első lépései
Kapcsolódás a Twilióhoz a globális IP, SMS és MMS-üzenetek küldése és fogadása. A Twilio a következőket teheti:

* A Twilio szolgáltatástól kapott az adatok alapján üzleti folyamatot hozhat létre. 
* Használjon, amely egy üzenetet, üzenetek listázása és egyéb műveleteket. Ezek a műveletek válaszol, és végezze el a kimenetet más műveletek esetében érhető el. Például amikor egy új Twilio-üzenetet kap, elvégezhető a szükséges ez az üzenet és a Service Bus munkafolyamat használatával. 

Első lépésként hozza létre a logikai alkalmazáshoz. Lásd: [hozzon létre egy logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-twilio"></a>Hozzon létre egy kapcsolatot a Twilióhoz
Ha ezt az összekötőt a logic apps hozzáad, adja meg a következő Twilio-értékeket:

| Tulajdonság | Szükséges | Leírás |
| --- | --- | --- |
| Fiókazonosító |Igen |Adja meg a Twilio-Fiókazonosító |
| Hozzáférési jogkivonat |Igen |Adja meg a Twilio hozzáférési jogkivonat |

> [!INCLUDE [Steps to create a connection to Twilio](../../includes/connectors-create-api-twilio.md)]
> 
> 

Ha a Twilio hozzáférési jogkivonat nem rendelkezik, tekintse meg [felhasználói identitás- és hozzáférési tokenek](https://www.twilio.com/docs/api/chat/guides/identity).

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Megtekintheti a valamennyi eseményindítót és műveletet a swaggerben meghatározott, és emellett a korlátozott a [összekötő részletei](/connectors/twilio/).

## <a name="more-connectors"></a>További összekötők
Lépjen vissza a [API-k listája](apis-list.md).