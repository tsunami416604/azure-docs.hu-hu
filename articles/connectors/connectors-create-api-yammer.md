---
title: Adja hozzá a Yammer-összekötő az Azure Logic Apps |} Microsoft Docs
description: A REST API-paraméterekkel rendelkező Yammer-összekötő áttekintése
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: b5ae0827-fbb3-45ec-8f45-ad1cc2e7eccc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 63f7b341b456d51cbde523684275a99632a672ed
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296475"
---
# <a name="get-started-with-the-yammer-connector"></a>A Yammer-összekötő az első lépései
A Yammer csatlakozni hozzáférés beszélgetések a vállalati hálózaton. A Yammer a következőket teheti:

* Az üzleti folyamat Yammer származó adatok alapján történő létrehozása. 
* Használja a váltja ki, ha van egy új üzenet, vagy adatcsatornára a következőket.
* Műveletek segítségével üzenetet, az összes üzenetet, és több. Ezeket a műveleteket válaszol, és végezze el a kimeneti más műveletek érhető el. Például egy új üzenet jelenik meg, ha az Office 365-tel e-mailt küldhet.

Első lépések egy logikai alkalmazás létrehozása most; Lásd: [logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-yammer"></a>Kapcsolatot létesíthet Yammer
A Yammer-összekötő használatához először létre kell hoznia egy **kapcsolat** adja meg a részleteket a tulajdonságok: 

| Tulajdonság | Szükséges | Leírás |
| --- | --- | --- |
| Jogkivonat |Igen |A Yammer hitelesítő adatainak megadása |

> [!INCLUDE [Steps to create a connection to Yammer](../../includes/connectors-create-api-yammer.md)]
> 

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Bármely eseményindítók és a swagger definiált műveletek megtekintése, és semmilyen határnak a Lásd még: a [connector részleteket](/connectors/yammer/).

## <a name="more-connectors"></a>További összekötők
Lépjen vissza a [API-k lista](apis-list.md).