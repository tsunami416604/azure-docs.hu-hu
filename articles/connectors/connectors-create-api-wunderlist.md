---
title: Wunderlist összekötő i n Azure logic apps |} Microsoft Docs
description: Kapcsolatot létesíthet Wunderlist, és ehhez a kapcsolathoz használni a logic apps a munkafolyamat létrehozásához.
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: e4773ecf-3ad3-44b4-a1b5-ee5f58baeadd
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 1ed9b19700157abca6e5ac4265f1e8c99a3d846d
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296373"
---
# <a name="get-started-with-the-wunderlist-connector"></a>A Wunderlist összekötő az első lépései
Wunderlist todo list és a feladat manager biztosíthat a felhasználóknak el tudják végezni feladataikat.  Egy bevásárlólista megosztja a projekten dolgozik, vagy a nyaralás tervezési, hogy Wunderlist egyszerűen rögzítése, megosztás, és fejezze be a lista elemeit. Wunderlist azonnal szinkronizálja a telefon, a tábla és a számítógép, között, hogy bárhonnan hozzáférhessen a feladatok.

Első lépések egy logikai alkalmazás létrehozása most; Lásd: [logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-wunderlist"></a>Kapcsolatot létesíthet Wunderlist
A Logic apps Wunderlist létrehozásához, létre kell hoznia egy **kapcsolat** adja meg a részleteket a következő tulajdonságokkal:

| Tulajdonság | Szükséges | Leírás |
| --- | --- | --- |
| Jogkivonat |Igen |A Wunderlist hitelesítő adatainak megadása |

Miután létrehozta a kapcsolatot, a műveletek végrehajtásához, és figyeljen az eseményindítók a használhatja.

> [!INCLUDE [Steps to create a connection to Wunderlist](../../includes/connectors-create-api-wunderlist.md)]
> 

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Bármely eseményindítók és a swagger definiált műveletek megtekintése, és semmilyen határnak a Lásd még: a [connector részleteket](/connectors/wunderlist/).

## <a name="more-connectors"></a>További összekötők
Lépjen vissza a [API-k lista](apis-list.md).