---
title: Kapcsolódás az Office 365 videóhoz
description: Videók kezelése az Office 365 videó REST API-kkal és Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 05/18/2016
tags: connectors
ms.openlocfilehash: bf3c7c4faac3fcd4dd0c3f368fbe18a224ed591b
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789484"
---
# <a name="get-started-with-the-office365-video-connector"></a>Ismerkedés a Office 365 videó-összekötővel

Kapcsolódjon az Office 365 videóhoz az Office 365-videóval kapcsolatos információk beszerzéséhez, a videók listájának lekéréséhez és egyebekhez. Az Office 365 videóval a következőket teheti:

* Hozza létre üzleti folyamatát az Office 365 videóból kapott adatok alapján. 

* Használjon olyan műveleteket, amelyek a videó-portál állapotát keresik, valamint a csatorna összes Videójának listáját, és így tovább. Ezek a műveletek választ kapnak, majd elérhetővé teszik a kimenetet más műveletekhez. 

A Bing Search-összekötővel például megkeresheti az Office 365-videókat, majd az Office 365 videó-összekötő használatával lekérheti a videóval kapcsolatos információkat. Ha a videó megfelel a követelményeknek, ezt a videót a Facebookon teheti közzé.

A logikai alkalmazások létrehozásának első lépéseiről a [logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)című témakörben olvashat.

## <a name="create-a-connection-to-office365-video-connector"></a>Csatlakozás a Office 365 video connectorhoz

Ha hozzáadja ezt az összekötőt a logikai alkalmazásokhoz, be kell jelentkeznie az Office 365 video-fiókjába, és engedélyeznie kell a Logic apps számára a fiókhoz való csatlakozást.

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]

A kapcsolatok létrehozása után adja meg az Office 365-videó tulajdonságait, például a bérlő nevét vagy a csatorna AZONOSÍTÓját. 

## <a name="connector-specific-details"></a>Összekötő-specifikus részletek

Megtekintheti a hencegés során definiált összes eseményindítót és műveletet, valamint az [összekötő részleteiben](/connectors/office365videoconnector/)megjelenő korlátokat is.

## <a name="next-steps"></a>Következő lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése