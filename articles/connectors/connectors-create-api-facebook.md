---
title: Kapcsolódás a Facebookhoz
description: Az ütemterv és az oldal kezelése a Facebook REST API-kkal és Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 1aa936f902dc17c9a401959c19824f6c581547b1
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789846"
---
# <a name="manage-your-facebook-timeline-and-page-by-using-azure-logic-apps"></a>Facebook-ütemterv és-oldal kezelése Azure Logic Apps használatával

Csatlakozhat a Facebookhoz, és közzéteheti az ütemtervet, beolvashatja az oldal hírcsatornáját és egyebeket. A Facebook használatával a következőket teheti:

* Hozza létre üzleti folyamatát a Facebookból kapott adatok alapján. 
* Trigger használata új bejegyzés érkezésekor.
* Az ütemtervre felvenni kívánt műveletek, az oldal hírcsatornáinak beszerzése és egyebek. Ezek a műveletek választ kapnak, majd elérhetővé teszik a kimenetet más műveletekhez. Ha például egy új bejegyzés van az idővonalon, akkor ezt a bejegyzést követheti, és leküldheti a Twitter-hírcsatornába. 

A logikai alkalmazások létrehozásának első lépéseiről a [logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)című témakörben olvashat.

## <a name="create-a-connection-to-facebook"></a>Facebook-kapcsolódás létrehozása

Ha hozzáadja ezt az összekötőt a logikai alkalmazásokhoz, engedélyezni kell a Logic apps-t a Facebookhoz való csatlakozáshoz.

1. Jelentkezzen be a Facebook-fiókjába.

2. Válassza az **Engedélyezés**lehetőséget, és engedélyezze a logikai alkalmazások számára a kapcsolódást és a Facebook használatát. 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 

## <a name="connector-reference"></a>Összekötő-referencia

A technikai részleteket, például az eseményindítókat, a műveleteket és a korlátozásokat az összekötő OpenAPI (korábban hencegő) fájljában leírtak szerint tekintse [meg az összekötő hivatkozási oldalát](/connectors/facebook/).

## <a name="next-steps"></a>Következő lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése