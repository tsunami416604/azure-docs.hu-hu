---
title: Facebook - Azure Logic Apps csatlakozni |} Microsoft Docs
description: Az ütemterv és a lap Facebook REST API-k és az Azure Logic Apps kezelése
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 11/07/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 985f3cf70a07b3080f34181e64c5bb1419d530bd
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295006"
---
# <a name="get-started-with-the-facebook-connector"></a>A Facebook-összekötő az első lépései
Csatlakozni a Facebookhoz és idővonalon, hírcsatorna oldal, és több. A Facebook a következőket teheti:

* Az üzleti folyamat Facebook származó adatok alapján történő létrehozása. 
* Egy eseményindító használni, új post fogadásakor.
* Küldje el a ütemterv használatát műveleteket hírcsatorna oldal, és több kapják meg. Ezeket a műveleteket válaszol, és végezze el a kimeneti más műveletek érhető el. Például, ha van egy új post az ütemterven, igénybe a feladás egy vagy több, és hogy a Twitter hírcsatorna. 

Most hozzon létre egy logic app kezdheti, lásd: [logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-facebook"></a>Kapcsolatot létesíthet a Facebook-on
Ezt az összekötőt a logic apps hozzáadásakor engedélyeznie kell a logic apps csatlakozni a Facebookhoz.

1. Jelentkezzen be a Facebook-fiókba
2. Válassza ki **engedélyezés**, és teszi lehetővé a logic Apps alkalmazásokat csatlakozzon, és használja a Facebook-on. 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 


## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Bármely eseményindítók és a swagger definiált műveletek megtekintése, és semmilyen határnak a Lásd még: a [connector részleteket](/connectors/facebook/).

## <a name="more-connectors"></a>További összekötők
Lépjen vissza a [API-k lista](apis-list.md).