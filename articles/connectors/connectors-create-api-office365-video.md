---
title: Csatlakozás az Office 365 Videóhoz
description: Az Office 365-videóban videókat kezelő feladatok és munkafolyamatok automatizálása az Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 05/18/2016
tags: connectors
ms.openlocfilehash: 8ac6b7b411e7f42dd076c5b16e7b500a819c617f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75665785"
---
# <a name="manage-videos-in-office365-video-by-using-azure-logic-apps"></a>Videók kezelése az Office365-videóban az Azure Logic Apps használatával

Az Office 365 Videó szolgáltatáshoz való csatlakozással információkat kaphat egy Office 365-videóról, videólistát és egyebeket kaphat. Az Office 365 Videó segítségével a következőket teheti:

* Az Office 365 Videó ból származó adatok alapján alakíthatja ki üzleti folyamatait. 

* Olyan műveleteket használj, amelyek ellenőrzik a videoportál állapotát, lekapják a csatorna összes videójának listáját stb. Ezek a műveletek választ kapnak, majd a kimenetet elérhetővé teszik más műveletek számára. 

A Bing Keresés összekötővel például office 365-ös videókat kereshet, majd az Office 365 videoösszekötősegítségével információkat kaphat a videóról. Ha a videó megfelel az igényeidnek, közzéteheted ezt a videót a Facebookon.

A kezdéshez hozzon létre egy logikai alkalmazást most, [lásd: Logikai alkalmazás létrehozása.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="connect-to-office365-video"></a>Csatlakozás az Office365 Videóhoz

Amikor hozzáadja ezt az összekötőt a logikai alkalmazásokhoz, be kell jelentkeznie az Office 365 Videó-fiókjába, és engedélyeznie kell, hogy a logikai alkalmazások csatlakozzanak a fiókjához.

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]

A kapcsolat létrehozása után adja meg az Office 365 videótulajdonságait, például a bérlő nevét vagy a csatornaazonosítót. 

## <a name="connector-specific-details"></a>Összekötő-specifikus részletek

Tekintse meg a swaggerben definiált eseményindítókat és műveleteket, és tekintse meg a korlátokat az [összekötő részleteiben.](/connectors/office365videoconnector/)

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)